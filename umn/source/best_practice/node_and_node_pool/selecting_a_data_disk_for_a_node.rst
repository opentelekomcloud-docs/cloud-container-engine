:original_name: cce_bestpractice_10012.html

.. _cce_bestpractice_10012:

Selecting a Data Disk for a Node
================================

When a node is created, a data disk is attached by default to its container runtime and kubelet. This data disk cannot be detached from the node, and its default space is 100 GiB. To cut costs, change the space of this disk to the minimum of 20 GiB or reduce the space of each common disk attached to the node to the minimum of 10 GiB.

.. important::

   Adjusting the space of the data disk used by the container runtime and kubelet carries certain risks. It is advised to perform a comprehensive assessment based on the estimation methods provided in this section before making any changes.

   -  A data disk that is too small may frequently run out of space, causing image-pull failures. If a node needs to pull different images frequently, reducing the data disk space is not recommended.
   -  During a cluster upgrade, the pre-check verifies whether data disk usage exceeds 95%. When disk pressure is high, the upgrade may be affected.
   -  Device Mapper-based systems are more prone to running out of space, so it is advised to use an OS with OverlayFS or choose a larger data disk.
   -  From a log-dumping perspective, application logs should be stored on a separate disk to prevent the dockersys volume from running out of space and impacting service operations.

   -  After reducing the data disk space, it is advised to install the CCE Node Problem Detector add-on in your cluster to detect potential disk-pressure issues on nodes so that you can be alerted in time. If disk-pressure issues occur, you can resolve them by referring to :ref:`What Can I Do If the Data Disk Space Is Insufficient? <cce_bestpractice_10012__section094517492470>`

Notes and Constraints
---------------------

-  Only clusters of v1.19 or later allow reducing the data disk space used by the container runtimes and kubelet on nodes.
-  Only the EVS disk space can be adjusted. Local disk space cannot be adjusted. Local disks are available only when the node specification is **disk-intensive** or **Ultra-high I/O**.

Selecting a Data Disk
---------------------

When selecting the appropriate data disk space, the following factors need to be considered:

-  During image pulling, the image .tar package is first downloaded from the image repository and then decompressed, after which the .tar package is deleted and only the image files are retained. During decompression, both the .tar package and the decompressed image files coexist, consuming additional storage space, which must be taken into account when estimating the required disk space.
-  During cluster creation, mandatory add-ons, such as CCE Container Storage (Everest) and CoreDNS, may be deployed on certain nodes. These add-ons occupy a certain amount of space, so approximately 2 GiB should be reserved for them.
-  During application runtime, logs are generated and consume storage space. To ensure stable service operation, approximately 1 GiB should be reserved for each pod.

For details about the calculation formulas, see :ref:`OverlayFS <cce_bestpractice_10012__section97181505230>` and :ref:`Device Mapper <cce_bestpractice_10012__section212373119234>`.

.. _cce_bestpractice_10012__section97181505230:

OverlayFS
---------

By default, on a node that uses OverlayFS, the container runtime and container image storage consume 90% of the data disk space (this ratio is recommended). This entire portion is allocated to the dockersys volume. The allocation is calculated as follows:

-  .. _cce_bestpractice_10012__li14531528269:

   Container runtime and image storage: 90% of the data disk space by default (Data disk space x 90%)

   -  dockersys volume (**/var/lib/docker**): The container runtime and image storage (90% by default) reside under **/var/lib/docker**. (Space = Data disk space x 90%)

-  Temporary kubelet and emptyDir storage: 10% of the data disk space (Data disk space x 10%)

On OverlayFS nodes, when pulling an image, the .tar package is downloaded first and then decompressed. During decompression, both the .tar package and the decompressed image files coexist in the dockersys volume, temporarily consuming roughly twice the actual image space. After decompression completes, the .tar package is deleted. Therefore, during image pulls, after subtracting the space used by system add-on images, the remaining dockersys space must be greater than twice the total image storage space. To ensure stable container operation, additional space should be reserved in the dockersys volume for container logs and other related files.

When selecting a data disk, consider the following:

dockersys volume space > The total actual image storage space x 2 + The total size of system add-on images (about 2 GiB) + The number of containers x The storage space required by a container (about 1 GiB for logs)

.. note::

   When container logs are output in the default json.log format, they consume space in the dockersys volume. If container logs are stored separately on persistent storage, they do not use dockersys space. Estimate the per-container space requirements as required.

Example:

Assume that a node uses OverlayFS and the data disk space is 20 GiB. Based on :ref:`the calculation above <cce_bestpractice_10012__li14531528269>`, the container runtime storage and image storage consume 90% of the data disk space, and the dockersys volume occupies 18 GiB (20 GiB x 90%). During cluster creation, mandatory system add-ons may use around 2 GiB of the space. If you need to deploy a 10-GiB image .tar package, decompressing it will consume roughly 20 GiB of dockersys space. When combined with the space used by mandatory add-ons, this exceeds the remaining dockersys space, making the image pull likely to fail.

.. _cce_bestpractice_10012__section212373119234:

Device Mapper
-------------

By default, on a node that uses Device Mapper, the container runtime and container image storage consume 90% of the data disk (this ratio is recommended). This entire portion includes both the dockersys volume and the thinpool volume. The allocation is calculated as follows:

-  .. _cce_bestpractice_10012__li1519941320114:

   Container runtime and image storage: 90% of the data disk space by default (Data disk space x 90%)

   -  dockersys volume (**/var/lib/docker**): 20% of the container runtime storage and image storage space (Data disk space x 90% x 20%)
   -  thinpool volume: 80% of the container runtime storage and image storage space (Data disk space x 90% x 80%)

-  Temporary kubelet and emptyDir storage: 10% of the data disk space (Data disk space x 10%)

When an image is pulled on a node that uses Device Mapper, the .tar package is first stored temporarily in the dockersys volume. After decompression, the image files are stored in the thinpool volume, and the .tar package in dockersys is deleted. Therefore, during image pulls, both the dockersys and thinpool volumes must have sufficient available space, with the understanding that dockersys is the smaller of the two. To ensure stable container operation, additional space should be reserved in the dockersys volume for container logs and other related files.

When selecting a data disk, consider the following:

-  dockersys volume space > The temporary storage required for the .tar package (approximately equal to the total actual image storage space) + The number of containers x The storage space required by a container (about 1 GiB for logs)
-  thinpool volume space > The actual total image storage space + The total size of system add-on images (about 2 GiB)

.. note::

   When container logs are output in the default json.log format, they consume space in the dockersys volume. If container logs are stored separately on persistent storage, they do not use dockersys space. Estimate the per-container space requirements as required.

Example:

Assume that a node uses Device Mapper and the data disk space is 20 GiB. Based on the :ref:`calculations above <cce_bestpractice_10012__li1519941320114>`, the container runtime and image storage consume 90% of the data disk space, and the dockersys volume occupies 3.6 GiB (20 GiB x 90% x 20%). During cluster creation, mandatory system add-ons may use around 2 GiB of the dockersys space, leaving approximately 1.6 GiB available. If you deploy an image .tar package larger than 1.6 GiB, the image pull may fail because dockersys does not have enough space to store the decompressed package, even if thinpool has sufficient space.

.. _cce_bestpractice_10012__section094517492470:

What Can I Do If the Data Disk Space Is Insufficient?
-----------------------------------------------------

**Solution 1: Clearing images**

Perform the following operations to clear unused images:

-  containerd nodes

   #. Obtain local images on the node.

      .. code-block::

         crictl images -v

   #. Delete the unnecessary images by image ID.

      .. code-block::

         crictl rmi {Image ID}

-  Docker nodes

   #. Obtain local images on the node.

      .. code-block::

         docker images

   #. Delete the unnecessary images by image ID.

      .. code-block::

         docker rmi {image-ID}

.. note::

   Do not delete system images, such as the **cce-pause** image. Otherwise, the pod creation may fail.

**Solution 2: Expanding the disk space**

Expand the data disk space as required. For details, see :ref:`Expanding the Storage Capacity <cce_bestpractice_00198>`.
