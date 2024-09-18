:original_name: cce_bestpractice_00198.html

.. _cce_bestpractice_00198:

Expanding the Storage Space
===========================

The storage classes that can be expanded for CCE nodes are as follows:

.. table:: **Table 1** Capacity expansion methods

   +-------------------+---------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
   | Type              | Name                | Purpose                                                                                                                                                   | Capacity Expansion Method                                                                                                              |
   +===================+=====================+===========================================================================================================================================================+========================================================================================================================================+
   | Node disk         | System disk         | A disk attached to a node for installing the operating system                                                                                             | :ref:`Expanding System Disk Capacity <cce_bestpractice_00198__en-us_topic_0196817407_section9671812194910>`                            |
   +-------------------+---------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
   |                   | Data disk           | The first data disk attached to a node for container engine and kubelet                                                                                   | :ref:`Expanding Data Disk Capacity <cce_bestpractice_00198__section1372753811531>`                                                     |
   +-------------------+---------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
   | Container storage | Pod container space | The base size of a container, which is, the upper limit of the disk space occupied by each pod (including the storage space occupied by container images) | :ref:`Expanding the Capacity of a Data Disk Used by Pod (basesize) <cce_bestpractice_00198__en-us_topic_0196817407_section3621141347>` |
   +-------------------+---------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+
   |                   | PVC                 | Storage resources mounted to the containers                                                                                                               | :ref:`Expanding a PVC <cce_bestpractice_00198__section1623491210348>`                                                                  |
   +-------------------+---------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_bestpractice_00198__en-us_topic_0196817407_section9671812194910:

Expanding System Disk Capacity
------------------------------

EulerOS 2.9 is used as the sample OS. There is only one partition (**/dev/vda1**) with a capacity of 50 GiB in the system disk **/dev/vda**, and then 50 GiB is added to the system disk. In this example, the additional 50 GiB is allocated to the existing **/dev/vda1** partition.

#. Expand the system disk capacity on the EVS console.

   Only the storage capacity of the EVS disk is expanded. You also need to perform the following steps to expand the partition and file system.

#. Log in to the node and run the **growpart** command to check whether growpart has been installed.

   If the tool operation guide is displayed, the growpart has been installed. Otherwise, run the following command to install growpart:

   .. code-block::

      yum install cloud-utils-growpart

#. Run the following command to view the total capacity of the system disk **/dev/vda**:

   .. code-block::

      fdisk -l

   If the following information is displayed, the total capacity of **/dev/vda** is 100 GiB.

   .. code-block:: console

      [root@test-48162 ~]# fdisk -l
      Disk /dev/vda: 100 GiB, 107374182400 bytes, 209715200 sectors
      Units: sectors of 1 * 512 = 512 bytes
      Sector size (logical/physical): 512 bytes / 512 bytes
      I/O size (minimum/optimal): 512 bytes / 512 bytes
      Disklabel type: dos
      Disk identifier: 0x78d88f0b

      Device     Boot Start       End   Sectors Size Id Type
      /dev/vda1  *     2048 104857566 104855519  50G 83 Linux

      Disk /dev/vdb: 100 GiB, 107374182400 bytes, 209715200 sectors
      Units: sectors of 1 * 512 = 512 bytes
      Sector size (logical/physical): 512 bytes / 512 bytes
      I/O size (minimum/optimal): 512 bytes / 512 bytes

      Disk /dev/mapper/vgpaas-dockersys: 90 GiB, 96632569856 bytes, 188735488 sectors
      Units: sectors of 1 * 512 = 512 bytes
      Sector size (logical/physical): 512 bytes / 512 bytes
      I/O size (minimum/optimal): 512 bytes / 512 bytes

      Disk /dev/mapper/vgpaas-kubernetes: 10 GiB, 10733223936 bytes, 20963328 sectors
      Units: sectors of 1 * 512 = 512 bytes
      Sector size (logical/physical): 512 bytes / 512 bytes
      I/O size (minimum/optimal): 512 bytes / 512 bytes

#. Run the following command to check the capacity of the system disk partition **/dev/vda1**:

   .. code-block::

      df -TH

   Information similar to the following is displayed:

   .. code-block:: console

      [root@test-48162 ~]# df -TH
      Filesystem                    Type      Size  Used Avail Use% Mounted on
      devtmpfs                      devtmpfs  1.8G     0  1.8G   0% /dev
      tmpfs                         tmpfs     1.8G     0  1.8G   0% /dev/shm
      tmpfs                         tmpfs     1.8G   13M  1.8G   1% /run
      tmpfs                         tmpfs     1.8G     0  1.8G   0% /sys/fs/cgroup
      /dev/vda1                     ext4      53G  3.3G   47G   7% /
      tmpfs                         tmpfs     1.8G   75M  1.8G   5% /tmp
      /dev/mapper/vgpaas-dockersys  ext4       95G  1.3G   89G   2% /var/lib/docker
      /dev/mapper/vgpaas-kubernetes ext4       11G   39M   10G   1% /mnt/paas/kubernetes/kubelet
      ...

#. Run the following command to extend the partition using growpart:

   .. code-block::

      growpart System disk Partition number

   The partition number is **1** because there is only one **/dev/vda1** partition in the system disk, as shown in the following command:

   .. code-block::

      growpart /dev/vda 1

   Information similar to the following is displayed:

   .. code-block::

      CHANGED: partition=1 start=2048 old: size=104855519 end=104857567 new: size=209713119 end=209715167

#. Run the following command to extend the file system:

   .. code-block::

      resize2fs Disk partition

   An example command is as follows:

   .. code-block::

      resize2fs /dev/vda1

   Information similar to the following is displayed:

   .. code-block::

      resize2fs 1.45.6 (20-Mar-2020)
      Filesystem at /dev/vda1 is mounted on /; on-line resizing required
      old_desc_blocks = 7, new_desc_blocks = 13
      The filesystem on /dev/vda1 is now 26214139 (4k) blocks long.

#. Run the following command to view the new capacity of the **/dev/vda1** partition:

   .. code-block::

      df -TH

   Information similar to the following is displayed:

   .. code-block:: console

      [root@test-48162 ~]# df -TH
      Filesystem                    Type      Size  Used Avail Use% Mounted on
      devtmpfs                      devtmpfs  1.8G     0  1.8G   0% /dev
      tmpfs                         tmpfs     1.8G     0  1.8G   0% /dev/shm
      tmpfs                         tmpfs     1.8G   13M  1.8G   1% /run
      tmpfs                         tmpfs     1.8G     0  1.8G   0% /sys/fs/cgroup
      /dev/vda1                     ext4     106G  3.3G   98G   4% /
      tmpfs                         tmpfs     1.8G   75M  1.8G   5% /tmp
      /dev/mapper/vgpaas-dockersys  ext4       95G  1.3G   89G   2% /var/lib/docker
      /dev/mapper/vgpaas-kubernetes ext4       11G   39M   10G   1% /mnt/paas/kubernetes/kubelet
      ...

#. Log in to the CCE console and click the cluster. In the navigation pane, choose **Nodes**. Click **More** > **Sync Server Data** in the row containing the target node.

.. _cce_bestpractice_00198__section1372753811531:

Expanding Data Disk Capacity
----------------------------

The first data disk of a CCE node is composed of container engine and kubelet space by default. If either of them reaches full capacity, you can expand the disk space as needed.

Expanding the Container Engine Capacity
---------------------------------------

The available container engine space affects image pulls and container startup and running. This section uses containerd as an example to describe how to expand the container engine capacity.

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

Expanding the kubelet Capacity
------------------------------

The kubelet space serves as a temporary storage location for kubelet components and EmptyDir. You can follow the following steps to increase the kubelet capacity:

#. Expand the capacity of a data disk on the EVS console.

   Only the storage capacity of the EVS disk is expanded. You also need to perform the following steps to expand the capacity of the logical volume and file system.

#. Log in to the CCE console and click the cluster. In the navigation pane, choose **Nodes**. Click **More** > **Sync Server Data** in the row containing the target node.

#. Log in to the target node.

#. Run **lsblk** to view the block device information of the node.

   .. code-block::

      # lsblk
      NAME                MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      vda                   8:0    0   50G  0 disk
      └─vda1                8:1    0   50G  0 part /
      vdb                   8:16   0  200G  0 disk      # Data disk has been expanded but not allocated
      ├─vgpaas-dockersys  253:0    0   90G  0 lvm  /var/lib/containerd            # Space used by the container engine
      └─vgpaas-kubernetes 253:1    0   10G  0 lvm  /mnt/paas/kubernetes/kubelet  # Space used by Kubernetes

#. Perform the following operations on the node to add the new disk capacity to the kubelet space:

   a. Expand the PV capacity so that LVM can identify the new EVS capacity. */dev/vdb* specifies the physical volume where kubelet is located.

      .. code-block::

         pvresize /dev/vdb

      Information similar to the following is displayed:

      .. code-block::

         Physical volume "/dev/vdb" changed
         1 physical volume(s) resized or updated / 0 physical volume(s) not resized

   b. Expand 100% of the free capacity to the logical volume. *vgpaas/kubernetes* specifies the logical volume used by kubelet.

      .. code-block::

         lvextend -l+100%FREE -n vgpaas/kubernetes

      Information similar to the following is displayed:

      .. code-block::

         Size of logical volume vgpaas/kubernetes changed from <10.00 GiB (2559 extents) to <110.00 GiB (28159 extents).
         Logical volume vgpaas/kubernetes successfully resized.

   c. Adjust the size of the file system. */dev/vgpaas/kubernetes* specifies the file system path of the container engine.

      .. code-block::

         resize2fs /dev/vgpaas/kubernetes

      Information similar to the following is displayed:

      .. code-block::

         Filesystem at /dev/vgpaas/kubernetes is mounted on /mnt/paas/kubernetes/kubelet; on-line resizing required
         old_desc_blocks = 2, new_desc_blocks = 14
         The filesystem on /dev/vgpaas/kubernetes is now 28834816 (4k) blocks long.

.. _cce_bestpractice_00198__en-us_topic_0196817407_section3621141347:

Expanding the Capacity of a Data Disk Used by Pod (basesize)
------------------------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. Choose **Nodes** from the navigation pane.

#. Click the Nodes tab, locate the row containing the target node, and choose **More** > **Reset Node** in the **Operation** column.

   .. important::

      Resetting a node may make unavailable the node-specific resources (such as local storage and workloads scheduled to this node). Exercise caution when performing this operation to avoid impact on running services.

#. Click **Yes**.

#. Reconfigure node parameters.

   If you need to adjust the container storage space, pay attention to the following configurations:

   **Storage Settings**: Click **Expand** next to the data disk to set the following parameters:

   **Space Allocation for Pods**: indicates the base size of a pod. It is the maximum size that a workload's pods (including the container images) can grow to in the disk space. Proper settings can prevent pods from taking all the disk space available and avoid service exceptions. It is recommended that the value is less than or equal to 80% of the container engine space. This parameter is related to the node OS and container storage rootfs and is not supported in some scenarios. For details, see :ref:`Data Disk Space Allocation <cce_10_0341>`.

#. After the node is reset, log in to the node and run the following command to access the container and check whether the container storage capacity has been expanded:

   **docker exec -it** *container_id* **/bin/sh** or **kubectl exec -it** *container_id* **/bin/sh**

   **df -h**

   |image1|

.. _cce_bestpractice_00198__section1623491210348:

Expanding a PVC
---------------

Cloud storage:

-  OBS and SFS: There is no storage restriction and capacity expansion is not required.
-  EVS:

   -  You can expand the capacity of automatically created volumes on the console. The procedure is as follows:

      #. Choose **Storage** in the navigation pane. In the right pane, click the **PVCs** tab. Click **More** in the **Operation** column of the target PVC and select **Scale-out**.
      #. Enter the capacity to be added and click **OK**.

-  For SFS Turbo, expand the capacity on the SFS console and then change the capacity in the PVC.

.. |image1| image:: /_static/images/en-us_image_0000001981275269.png
