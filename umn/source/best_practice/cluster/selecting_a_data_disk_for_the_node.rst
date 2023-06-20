:original_name: cce_bestpractice_10012.html

.. _cce_bestpractice_10012:

Selecting a Data Disk for the Node
==================================

When a node is created, a data disk is created by default for container runtime and kubelet components to use. The data disk used by the container runtime and kubelet components cannot be detached, and the default size is 100 GB. To reduce costs, the size of a common data disk attached to a node can be reduced to 10 GB.

.. important::

   Adjusting the size of the data disk used by the container and Kubelet component may cause risks. You are advised to adjust the size after comprehensive evaluation based on the estimation method provided in this section.

   -  If the data disk capacity is too small, the disk space may be insufficient. As a result, the image fails to be pulled. If different images need to be frequently pulled on the node, you are not advised to reduce the data disk capacity.
   -  During the cluster upgrade pre-check, the system checks whether the data disk usage exceeds 95%. If the disk pressure is high, the cluster upgrade may be affected.
   -  If Device Mapper is used, the disk space may be insufficient. You are advised to use the OverlayFS or select a large-capacity data disk.
   -  For dumping logs, application logs must be stored in a separate disk to prevent insufficient space of the dockersys partition from affecting service running.

   -  After reducing the data disk capacity, you are advised to install a npd add-on in the cluster to detect node disk pressure. If the disk pressure of a node is high, rectify the fault by referring to :ref:`What Do I Do If the Data Disk Space Is Insufficient? <cce_bestpractice_10012__section094517492470>`.

Notes and Constraints
---------------------

-  Only clusters of v1.19 or later support reducing the data disk capacity used by running containers and kubelet components.
-  Only the EVS disk capacity can be adjusted. (Local disks are available only when the node specification is **disk-intensive** or **Ultra-high I/O**.)

Selecting a Data Disk
---------------------

When selecting a proper data disk, consider the following factors:

-  During image pulling, the system downloads the image .tar package from the image repository, decompresses the package, and deletes the TAR package to retain the image file. During the decompression of the .tar package, the .tar package and the decompressed image file coexist, occupying extra storage space. Pay attention to this when calculating the required data disk capacity.
-  During cluster creation, mandatory add-ons (such as everest and coredns add-ons) may be deployed on nodes, occupying certain space. When calculating the data disk size, reserve about 2 GB space for them.
-  Logs are generated during application running and occupy certain space. To ensure normal service running, reserve about 1 GB space for each pod.

For details about the calculation formulas, see :ref:`OverlayFS <cce_bestpractice_10012__section97181505230>` and :ref:`Device Mapper <cce_bestpractice_10012__section212373119234>`.

.. _cce_bestpractice_10012__section97181505230:

OverlayFS
---------

By default, the container engine and container image space on OverlayFS nodes occupy 90% of the data disk space (you are advised to retain this value). All the space is used for the dockersys partition. The calculation formula is as follows:

-  .. _cce_bestpractice_10012__li14531528269:

   Container engine and container image space: Defaults to 90%. Space size = Data disk space x 90%

   -  dockersys partition (**/var/lib/docker** path): The entire container engine and container image space (90% of the data disk by default) are in the **/var/lib/docker** directory. Space size = Data disk space x 90%

-  kubelet components and emptyDir volumes space: 10% of the data disk space. Space size = Data disk space x 10%

On an OverlayFS node, when an image is pulled, the .tar package is decompressed after being downloaded. During this process, the .tar package and the decompressed image file are stored in the dockersys space at the same time, occupying about twice the actual image capacity, after the decompression is complete, the .tar package is deleted. Therefore, in the actual image pulling process, after deducting the space occupied by the system add-on image, ensure that the remaining space of the dockersys partition is greater than twice the actual image capacity. To ensure that the container can run properly, you need to reserve pod space in the dockersys partition for container logs and other related files.

A proper data disk should meet the following formula:

**dockersys partition capacity > 2 x Actual total image capacity + Total system add-on image capacity (about 2 GB) + Number of containers x Available space for a single container (about 1 GB log space)**

.. note::

   If container logs are output in the json.log format, the dockersys partition is occupied. If persistent storage is configured for container logs, the dockersys partition is not occupied. Estimate the space of a single container as required.

Example:

Assume that the storage type of the node is OverlayFS and the data disk size of the node is 20 GB. According to :ref:`the preceding formula <cce_bestpractice_10012__li14531528269>`, the default ratio of the container engine and image space is 90%. Therefore, the dockersys partition disk usage is 18 GB (20 GB x 90%). In addition, mandatory add-ons may occupy about 2 GB space during cluster creation. If you deploy a .tar image package of 10 GB, 20 GB dockersys space is occupied by the decompressed package. In addition, the space occupied by mandatory add-ons exceeds the remaining space of dockersys. As a result, the image may fail to be pulled.

.. _cce_bestpractice_10012__section212373119234:

Device Mapper
-------------

By default, the container engine and image space on Device Mapper nodes occupy 90% of the data disk space (recommended). This 90% capacity is divided into the dockersys partition and thinpool space. The calculation formula is as follows:

-  .. _cce_bestpractice_10012__li1519941320114:

   Container engine and image space: 90% of the data disk space by default. Space size = Data disk space x 90%

   -  dockersys partition (**/var/lib/docker** path): Defaults to 20%. Space size = Data disk space x 90% x 20%
   -  thinpool space: Defaults to 80%. Space size = Data disk space x 90% x 80%

-  kubelet and emptyDir space: occupy 10%. Space size = Data disk space x 10%

On a Device Mapper node, when an image is pulled, the TAR package is temporarily stored in the dockersys partition. After the TAR package is decompressed, the image file is stored in the thinpool space. Finally, the TAR package in the dockersys space is deleted. Therefore, during image pulling, ensure that the dockersys partition space and thinpool space are sufficient. The dockersys space is smaller than the thinpool space. Pay attention to this when calculating the data disk space. To ensure that a container can run properly, reserve pod space in the dockersys partition to store container logs and other related files.

A proper data disk should meet the following formula:

-  **dockersys partition capacity > temporary storage space of the TAR package (approximately equal to the actual total image capacity) + Number of containers x Space of a single container (about 1 GB log space must be reserved for each container)**
-  **thinpool space > Actual total image capacity + Total add-on image capacity (about 2 GB)**

.. note::

   If container logs are output in the json.log format, the dockersys partition is occupied. If persistent storage is configured for container logs, the dockersys partition is not occupied. Estimate the space of a single container as required.

Example:

Assume that the storage type of the node is Device Mapper and the data disk size of the node is 20 GB. According to :ref:`the preceding formula <cce_bestpractice_10012__li1519941320114>`, the default ratio of the container engine and image space is 90%. Therefore, the dockersys partition disk usage is: 20 GB x 90% x 20% = 3.6 GB. In addition, mandatory add-ons may occupy about 2 GB dockersys space during cluster creation. Therefore, the remaining space is about 1.6 GB. If you deploy a .tar image package larger than 1.6 GB, the dockersys partition space is insufficient when the package is decompressed. As a result, the image may fail to be pulled.

.. _cce_bestpractice_10012__section094517492470:

What Do I Do If the Data Disk Space Is Insufficient?
----------------------------------------------------

**Solution 1: Clearing Images**

You can run the following command to clear unused Docker images:

.. code-block::

   docker system prune -a

.. note::

   This command will delete all Docker images that are not used. Exercise caution when running this command.

**Solution 2: Expanding the Disk Capacity**

#. Expand the capacity of the data disk on the EVS console.

#. Log in to the CCE console and click the cluster. In the navigation pane, choose **Nodes**. Click **More** > **Sync Server Data** at the row containing the target node.

#. Log in to the target node.

#. Run the **lsblk** command to check the block device information of the node.

   A data disk is divided depending on the container storage **Rootfs**:

   -  Overlayfs: No independent thin pool is allocated. Image data is stored in the **dockersys** disk.

      .. code-block::

         # lsblk
         NAME                MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
         sda                   8:0    0   50G  0 disk
         └─sda1                8:1    0   50G  0 part /
         sdb                   8:16   0  200G  0 disk
         ├─vgpaas-dockersys  253:0    0   90G  0 lvm  /var/lib/docker               # Space used by Docker.
         └─vgpaas-kubernetes 253:1    0   10G  0 lvm  /mnt/paas/kubernetes/kubelet  # Space used by Kubernetes.

      Run the following commands on the node to add the new disk capacity to the **dockersys** disk:

      .. code-block::

         pvresize /dev/sdb
         lvextend -l+100%FREE -n vgpaas/dockersys
         resize2fs /dev/vgpaas/dockersys

   -  Devicemapper: A thin pool is allocated to store image data.

      .. code-block::

         # lsblk
         NAME                                MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
         sda                                   8:0    0   50G  0 disk
         └─sda1                                8:1    0   50G  0 part /
         sdb                                   8:16   0  200G  0 disk
         ├─vgpaas-dockersys                  253:0    0   18G  0 lvm  /var/lib/docker
         ├─vgpaas-thinpool_tmeta             253:1    0    3G  0 lvm
         │ └─vgpaas-thinpool                 253:3    0   67G  0 lvm                   # Thin pool space.
         │   ...
         ├─vgpaas-thinpool_tdata             253:2    0   67G  0 lvm
         │ └─vgpaas-thinpool                 253:3    0   67G  0 lvm
         │   ...
         └─vgpaas-kubernetes                 253:4    0   10G  0 lvm  /mnt/paas/kubernetes/kubelet

      -  Run the following commands on the node to add the new disk capacity to the **thinpool** disk:

         .. code-block::

            pvresize /dev/sdb
            lvextend -l+100%FREE -n vgpaas/thinpool

      -  Run the following commands on the node to add the new disk capacity to the **dockersys** disk:

         .. code-block::

            pvresize /dev/sdb
            lvextend -l+100%FREE -n vgpaas/dockersys
            resize2fs /dev/vgpaas/dockersys
