:original_name: cce_bestpractice_10012.html

.. _cce_bestpractice_10012:

Selecting a Data Disk for the Node
==================================

When a node is created, a data disk is attached by default for a container runtime and kubelet. The data disk used by the container runtime and kubelet cannot be detached, and the default capacity is 100 GiB. To cut costs, you can adjust the disk capacity to the minimum of 20 GiB or reduce the disk capacity attached to a node to the minimum of 10 GiB.

.. important::

   Adjusting the size of the data disk used by the container runtime and kubelet may incur risks. You are advised to evaluate the capacity adjustment and then perform the operations described in this section.

   -  If the disk capacity is too small, the image pull may fail. If different images need to be frequently pulled on the node, you are not advised to reduce the data disk capacity.
   -  Before a cluster upgrade, the system checks whether the data disk usage exceeds 95%. If the usage is high, the cluster upgrade may be affected.
   -  If Device Mapper is used, the disk capacity may be insufficient. You are advised to use the OverlayFS or select a large-capacity data disk.
   -  For dumping logs, application logs must be stored in a separate disk to prevent insufficient storage capacity of the dockersys volume from affecting service running.

   -  After reducing the data disk capacity, you are advised to install the npd add-on in the cluster to detect disk usage. If the disk usage of a node is high, resolve this problem by referring to :ref:`What If the Data Disk Capacity Is Insufficient? <cce_bestpractice_10012__section094517492470>`

Constraints
-----------

-  Only clusters of v1.19 or later allow reducing the capacity of the data disk used by container runtimes and kubelet.
-  Only the EVS disk capacity can be adjusted. (Local disks are available only when the node specification is **disk-intensive** or **Ultra-high I/O**.)

Selecting a Data Disk
---------------------

When selecting a data disk, consider the following factors:

-  During image pull, the system downloads the image package (the .tar package) from the image repository, and decompresses the package. Then it deletes the package but retain the image file. During the decompression of the .tar package, the package and the decompressed image file coexist. Reserve the capacity for the decompressed files.
-  Mandatory add-ons (such as everest and coredns) may be deployed on nodes during cluster creation. When calculating the data disk size, reserve about 2 GiB storage capacity for them.
-  Logs are generated during application running. To ensure stable application running, reserve about 1 GiB storage capacity for each pod.

For details about the calculation formulas, see :ref:`OverlayFS <cce_bestpractice_10012__section97181505230>` and :ref:`Device Mapper <cce_bestpractice_10012__section212373119234>`.

.. _cce_bestpractice_10012__section97181505230:

OverlayFS
---------

By default, the container engine and container image storage capacity of a node using the OverlayFS storage driver occupies 90% of the data disk capacity (you are advised to retain this value). All the 90% storage capacity is used for dockersys partitioning. The calculation methods are as follows:

-  .. _cce_bestpractice_10012__li14531528269:

   Capacity for storing container engines and container images requires 90% of the data disk capacity by default.

   -  Capacity for dockersys volume (in the **/var/lib/docker** directory) requires 90% of the data disk capacity. The entire container engine and container image capacity (need 90% of the data disk capacity by default) are in the **/var/lib/docker** directory.

-  Capacity for storing temporary kubelet and emptyDir requires 10% of the data disk capacity.

On a node using the OverlayFS, when an image is pulled, the .tar package is decompressed after being downloaded. During this process, the .tar package and the decompressed image file are stored in the dockersys volume, occupying about twice the actual image storage capacity. After the decompression is complete, the .tar package is deleted. Therefore, during image pull, after deducting the storage capacity occupied by the system add-on images, ensure that the remaining capacity of the dockersys volume is greater than twice the actual image storage capacity. To ensure that the containers can run stably, reserve certain capacity in the dockersys volume for container logs and other related files.

When selecting a data disk, consider the following formula:

**Capacity of dockersys volume > Actual total image storage capacity x 2 + Total system add-on image storage capacity (about 2 GiB) + Number of containers x Available storage capacity for a single container (about 1 GiB log storage capacity for each container)**

.. note::

   If container logs are output in the **json.log** format, they will occupy some capacity in the dockersys volume. If container logs are stored on persistent storage, they will not occupy capacity in the dockersys volume. Estimate the capacity of every container as required.

Example:

Assume that the node uses the OverlayFS and the data disk attached to this node is 20 GiB. According to :ref:`the preceding methods <cce_bestpractice_10012__li14531528269>`, the capacity for storing container engines and images occupies 90% of the data disk capacity, and the capacity for the dockersys volume is 18 GiB (20 GiB x 90%). Additionally, mandatory add-ons may occupy about 2 GiB storage capacity during cluster creation. If you deploy a .tar package of 10 GiB, the package decompression takes 20 GiB of the dockersys volume's storage capacity. This, coupled with the storage capacity occupied by mandatory add-ons, exceeds the remaining capacity of the dockersys volume. As a result, the image pull may fail.

.. _cce_bestpractice_10012__section212373119234:

Device Mapper
-------------

By default, the capacity for storing container engines and container images of a node using the Device Mapper storage driver occupies 90% of the data disk capacity (you are advised to retain this value). The occupied capacity includes the dockersys volume and thinpool volume. The calculation methods are as follows:

-  .. _cce_bestpractice_10012__li1519941320114:

   Capacity for storing container engines and container images requires 90% of the data disk capacity by default.

   -  Capacity for the dockersys volume (in the **/var/lib/docker** directory) requires 20% of the capacity for storing container engines and container images.
   -  Capacity forthe thinpool volume requires 80% of the container engine and container image storage capacity.

-  Capacity for storing temporary kubelet and emptyDir requires 10% of the data disk capacity.

On a node using the Device Mapper storage driver, when an image is pulled, the .tar package is temporarily stored in the dockersys volume. After the .tar package is decompressed, the image file is stored in the thinpool volume, and the package in the dockersys volume will be deleted. Therefore, during image pull, ensure that the storage capacity of the dockersys volume and thinpool volume are sufficient, and note that the former is smaller than the latter. To ensure that the containers can run stably, reserve certain capacity in the dockersys volume for container logs and other related files.

When selecting a data disk, consider the following formulas:

-  **Capacity for dockersys volume > Temporary storage capacity of the .tar package (approximately equal to the actual total image storage capacity) + Number of containers x Storage capacity of a single container (about 1 GiB log storage capacity must be reserved for each container)**
-  **Capacity for thinpool volume > Actual total image storage capacity + Total add-on image storage capacity (about 2 GiB)**

.. note::

   If container logs are output in the **json.log** format, they will occupy some capacity in the dockersys volume. If container logs are stored on persistent storage, they will not occupy capacity in the dockersys volume. Estimate the capacity of every container as required.

Example:

Assume that the node uses the Device Mapper and the data disk attached to this node is 20 GiB. According to :ref:`the preceding methods <cce_bestpractice_10012__li1519941320114>`, the container engine and image storage capacity occupies 90% of the data disk capacity, and the disk usage of the dockersys volume is 3.6 GiB. Additionally, the storage capacity of the mandatory add-ons may occupy about 2 GiB of the dockersys volume during cluster creation. The remaining storage capacity is about 1.6 GiB. If you deploy a .tar image package larger than 1.6 GiB, the storage capacity of the dockersys volume is insufficient for the package to be decompressed. As a result, the image pull may fail.

.. _cce_bestpractice_10012__section094517492470:

What If the Data Disk Capacity Is Insufficient?
-----------------------------------------------

**Solution 1: Clearing images**

Perform the following operations to clear unused images:

-  Nodes that use containerd

   #. Obtain local images on the node.

      .. code-block::

         crictl images -v

   #. Delete the images that are not required by image ID.

      .. code-block::

         crictl rmi Image ID

-  Nodes that use Docker

   #. Obtain local images on the node.

      .. code-block::

         docker images

   #. Delete the images that are not required by image ID.

      .. code-block::

         docker rmi Image ID

.. note::

   Do not delete system images such as the cce-pause image. Otherwise, pods may fail to be created.

**Solution 2: Expanding the disk capacity**

#. Expand the capacity of a data disk on the EVS console.

   Only the storage capacity of the EVS disk is expanded. You also need to perform the following steps to expand the capacity of the logical volume and file system.

#. Log in to the CCE console and click the cluster. In the navigation pane, choose **Nodes**. Click **More** > **Sync Server Data** in the row containing the target node.

#. Log in to the target node.

#. Run the **lsblk** command to check the block device information of the node.

   A data disk is divided depending on the container storage **Rootfs**:

   Overlayfs: No independent thin pool is allocated. Image data is stored in **dockersys**.

   a. Check the disk and partition sizes of the device.

      .. code-block::

         # lsblk
         NAME                MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
         vda                   8:0    0   50G  0 disk
         └─vda1                8:1    0   50G  0 part /
         vdb                   8:16   0  200G  0 disk      # Data disk has been expanded but not allocated
         ├─vgpaas-dockersys  253:0    0   90G  0 lvm  /var/lib/containerd          # Space used by the container engine
         └─vgpaas-kubernetes 253:1    0   10G  0 lvm  /mnt/paas/kubernetes/kubelet  # Space used by Kubernetes

   b. Expand the disk capacity.

      Add the new disk capacity to the **dockersys** logical volume used by the container engine.

      #. Expand the PV capacity so that LVM can identify the new EVS capacity. */dev/vdb* specifies the physical volume where dockersys is located.

         .. code-block::

            pvresize /dev/vdb

         Information similar to the following is displayed:

         .. code-block::

            Physical volume "/dev/vdb" changed
            1 physical volume(s) resized or updated / 0 physical volume(s) not resized

      #. Expand 100% of the free capacity to the logical volume. *vgpaas/dockersys* specifies the logical volume used by the container engine.

         .. code-block::

            lvextend -l+100%FREE -n vgpaas/dockersys

         Information similar to the following is displayed:

         .. code-block::

            Size of logical volume vgpaas/dockersys changed from <90.00 GiB (23039 extents) to <190.00 GiB (48639 extents).
            Logical volume vgpaas/dockersys successfully resized.

      #. Adjust the size of the file system. */dev/vgpaas/dockersys* specifies the file system path of the container engine.

         .. code-block::

            resize2fs /dev/vgpaas/dockersys

         Information similar to the following is displayed:

         .. code-block::

            Filesystem at /dev/vgpaas/dockersys is mounted on /var/lib/containerd; on-line resizing required
            old_desc_blocks = 12, new_desc_blocks = 24
            The filesystem on /dev/vgpaas/dockersys is now 49807360 (4k) blocks long.

   Devicemapper: A thin pool is allocated to store image data.

   a. Check the disk and partition sizes of the device.

      .. code-block::

         # lsblk
         NAME                                MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
         vda                                   8:0    0   50G  0 disk
         └─vda1                                8:1    0   50G  0 part /
         vdb                                   8:16   0  200G  0 disk
         ├─vgpaas-dockersys                  253:0    0   18G  0 lvm  /var/lib/docker
         ├─vgpaas-thinpool_tmeta             253:1    0    3G  0 lvm
         │ └─vgpaas-thinpool                 253:3    0   67G  0 lvm                   # Space used by thinpool
         │   ...
         ├─vgpaas-thinpool_tdata             253:2    0   67G  0 lvm
         │ └─vgpaas-thinpool                 253:3    0   67G  0 lvm
         │   ...
         └─vgpaas-kubernetes                 253:4    0   10G  0 lvm  /mnt/paas/kubernetes/kubelet

   b. Expand the disk capacity.

      Option 1: Add the new disk capacity to the thin pool disk.

      #. Expand the PV capacity so that LVM can identify the new EVS capacity. */dev/vdb* specifies the physical volume where thinpool is located.

         .. code-block::

            pvresize /dev/vdb

         Information similar to the following is displayed:

         .. code-block::

            Physical volume "/dev/vdb" changed
            1 physical volume(s) resized or updated / 0 physical volume(s) not resized

      #. Expand 100% of the free capacity to the logical volume. *vgpaas/thinpool* specifies the logical volume used by the container engine.

         .. code-block::

            lvextend -l+100%FREE -n vgpaas/thinpool

         Information similar to the following is displayed:

         .. code-block::

            Size of logical volume vgpaas/thinpool changed from <67.00 GiB (23039 extents) to <167.00 GiB (48639 extents).
            Logical volume vgpaas/thinpool successfully resized.

      #. Do not need to adjust the size of the file system, because the thin pool is not mounted to any devices.

      Option 2: Add the new disk capacity to the **dockersys** disk.

      #. Expand the PV capacity so that LVM can identify the new EVS capacity. */dev/vdb* specifies the physical volume where dockersys is located.

         .. code-block::

            pvresize /dev/vdb

         Information similar to the following is displayed:

         .. code-block::

            Physical volume "/dev/vdb" changed
            1 physical volume(s) resized or updated / 0 physical volume(s) not resized

      #. Expand 100% of the free capacity to the logical volume. *vgpaas/dockersys* specifies the logical volume used by the container engine.

         .. code-block::

            lvextend -l+100%FREE -n vgpaas/dockersys

         Information similar to the following is displayed:

         .. code-block::

            Size of logical volume vgpaas/dockersys changed from <18.00 GiB (7679 extents) to <118.00 GiB (33279 extents).
            Logical volume vgpaas/dockersys successfully resized.

      #. Adjust the size of the file system. */dev/vgpaas/dockersys* specifies the file system path of the container engine.

         .. code-block::

            resize2fs /dev/vgpaas/dockersys

         Information similar to the following is displayed:

         .. code-block::

            Filesystem at /dev/vgpaas/dockersys is mounted on /var/lib/docker; on-line resizing required
            old_desc_blocks = 4, new_desc_blocks = 16
            The filesystem on /dev/vgpaas/dockersys is now 49807360 (4k) blocks long.
