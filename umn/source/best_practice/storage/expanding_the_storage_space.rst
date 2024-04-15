:original_name: cce_bestpractice_00198.html

.. _cce_bestpractice_00198:

Expanding the Storage Space
===========================

The storage classes that can be expanded for CCE nodes are as follows:

.. table:: **Table 1** Capacity expansion methods

   +-------------------+---------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------+
   | Type              | Name                | Purpose                                                                                                                                                   | Capacity Expansion Method                                                                                                                      |
   +===================+=====================+===========================================================================================================================================================+================================================================================================================================================+
   | Node disk         | System disk         | A disk attached to a node for installing the operating system                                                                                             | :ref:`Expanding System Disk Capacity <cce_bestpractice_00198__en-us_topic_0196817407_section9671812194910>`                                    |
   +-------------------+---------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------+
   |                   | Data disk           | A disk that must be attached to a node for the container engine and kubelet                                                                               | -  :ref:`Expanding the Capacity of a Data Disk Used by Container Engines <cce_bestpractice_00198__en-us_topic_0196817407_section155006183017>` |
   |                   |                     |                                                                                                                                                           | -  :ref:`Expanding the Capacity of a Data Disk Used by kubelet <cce_bestpractice_00198__section181915191968>`                                  |
   +-------------------+---------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------+
   | Container storage | Pod container space | The base size of a container, which is, the upper limit of the disk space occupied by each pod (including the storage space occupied by container images) | :ref:`Expanding the Capacity of a Data Disk Used by Pod (basesize) <cce_bestpractice_00198__en-us_topic_0196817407_section3621141347>`         |
   +-------------------+---------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------+
   |                   | PVC                 | Storage resources mounted to the containers                                                                                                               | :ref:`Expanding a PVC <cce_bestpractice_00198__section1623491210348>`                                                                          |
   +-------------------+---------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_bestpractice_00198__en-us_topic_0196817407_section9671812194910:

Expanding System Disk Capacity
------------------------------

EulerOS 2.9 is used as the sample OS. There is only one partition (**/dev/vda1**) with a capacity of 50 GiB in the system disk **/dev/vda**, and then 50 GiB is added to the system disk. In this example, the additional 50 GiB is allocated to the existing **/dev/vda1** partition.

#. Expand the capacity of the system disk on the EVS console.

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

.. _cce_bestpractice_00198__en-us_topic_0196817407_section155006183017:

Expanding the Capacity of a Data Disk Used by Container Engines
---------------------------------------------------------------

CCE divides the data disk space for two parts by default. One part is used to store the Docker/containerd working directories, container images, and image metadata. The other is reserved for kubelet and emptyDir volumes. The available container engine space affects image pulls and container startup and running. This section uses Docker as an example to describe how to expand the container engine capacity.

#. Expand the capacity of the data disk on the EVS console.

#. Log in to the CCE console and click the cluster. In the navigation pane, choose **Nodes**. Click **More** > **Sync Server Data** in the row containing the target node.

#. Log in to the target node.

#. Run **lsblk** to view the block device information of the node.

   A data disk is divided depending on the container storage **Rootfs**:

   -  Overlayfs: No independent thin pool is allocated. Image data is stored in the **dockersys** disk.

      .. code-block::

         # lsblk
         NAME                MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
         vda                   8:0    0   50G  0 disk
         └─vda1                8:1    0   50G  0 part /
         vdb                   8:16   0  200G  0 disk
         ├─vgpaas-dockersys  253:0    0   90G  0 lvm  /var/lib/docker               # Space used by the container engine
         └─vgpaas-kubernetes 253:1    0   10G  0 lvm  /mnt/paas/kubernetes/kubelet  # Space used by Kubernetes

      Run the following commands on the node to add the new disk capacity to the **dockersys** disk:

      .. code-block::

         pvresize /dev/vdb
         lvextend -l+100%FREE -n vgpaas/dockersys
         resize2fs /dev/vgpaas/dockersys

   -  Devicemapper: A thin pool is allocated to store image data.

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

      -  Run the following commands on the node to add the new disk capacity to the **thinpool** disk:

         .. code-block::

            pvresize /dev/vdb
            lvextend -l+100%FREE -n vgpaas/thinpool

      -  Run the following commands on the node to add the new disk capacity to the **dockersys** disk:

         .. code-block::

            pvresize /dev/vdb
            lvextend -l+100%FREE -n vgpaas/dockersys
            resize2fs /dev/vgpaas/dockersys

.. _cce_bestpractice_00198__section181915191968:

Expanding the Capacity of a Data Disk Used by kubelet
-----------------------------------------------------

CCE divides the data disk space for two parts by default. One part is used to store the Docker/containerd working directories, container images, and image metadata. The other is reserved for kubelet and emptyDir volumes. To expand the kubelet space, perform the following steps:

#. Expand the capacity of the data disk on the EVS console.

#. Log in to the CCE console and click the cluster. In the navigation pane, choose **Nodes**. Click **More** > **Sync Server Data** in the row containing the target node.

#. Log in to the target node.

#. Run **lsblk** to view the block device information of the node.

   .. code-block::

      # lsblk
      NAME                MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      vda                   8:0    0   50G  0 disk
      └─vda1                8:1    0   50G  0 part /
      vdb                   8:16   0  200G  0 disk
      ├─vgpaas-dockersys  253:0    0   90G  0 lvm  /var/lib/docker               # Space used by the container engine
      └─vgpaas-kubernetes 253:1    0   10G  0 lvm  /mnt/paas/kubernetes/kubelet  # Space used by Kubernetes

#. Run the following commands on the node to add the new disk capacity to the Kubernetes disk:

   .. code-block::

      pvresize /dev/vdb
      lvextend -l+100%FREE -n vgpaas/kubernetes
      resize2fs /dev/vgpaas/kubernetes

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

   |image1|

   **Storage Settings**: Click **Expand** next to the data disk to set the following parameters:

   -  **Allocate Disk Space**: storage space used by the container engine to store the Docker/containerd working directory, container image data, and image metadata. Defaults to 90% of the data disk.
   -  **Allocate Pod Basesize**: CCE allows you to set an upper limit for the disk space occupied by each workload pod (including the space occupied by container images). This setting prevents the pods from taking all the disk space available, which may cause service exceptions. It is recommended that the value be smaller than or equal to 80% of the container engine space.

      .. note::

         -  The capability of customizing pod basesize is related to the node OS and container storage rootfs.

            -  When the rootfs uses Device Mapper, the node supports custom pod basesize. The default storage space of a single container is 10 GiB.

            -  When the rootfs uses OverlayFS, most nodes do not support custom pod basesize. The storage space of a single container is not limited and defaults to the container engine space.

               Only EulerOS 2.9 nodes in clusters of 1.19.16, 1.21.3, 1.23.3, and later versions support custom pod basesize.

         -  In the case of using Docker on EulerOS 2.9 nodes, **basesize** will not take effect if **CAP_SYS_RESOURCE** or **privileged** is configured for a container.

#. After the node is reset, log in to the node and run the following command to access the container and check whether the container storage capacity has been expanded:

   **docker exec -it** *container_id* **/bin/sh** or **kubectl exec -it** *container_id* **/bin/sh**

   **df -h**

   |image2|

.. _cce_bestpractice_00198__section1623491210348:

Expanding a PVC
---------------

Cloud storage:

-  OBS and SFS: There is no storage restriction and capacity expansion is not required.
-  EVS:

   -  You can expand the capacity of automatically created pay-per-use volumes on the console. The procedure is as follows:

      #. Choose **Storage** in the navigation pane and click the **PersistentVolumeClaims (PVCs)** tab. Click **More** in the **Operation** column of the target PVC and select **Scale-out**.
      #. Enter the capacity to be added and click **OK**.

-  For SFS Turbo, expand the capacity on the SFS console and then change the capacity in the PVC.

.. |image1| image:: /_static/images/en-us_image_0000001797909113.png
.. |image2| image:: /_static/images/en-us_image_0000001797870097.png
