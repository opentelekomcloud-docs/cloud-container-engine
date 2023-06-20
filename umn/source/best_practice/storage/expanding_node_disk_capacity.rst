:original_name: cce_bestpractice_00198.html

.. _cce_bestpractice_00198:

Expanding Node Disk Capacity
============================

System Disk
-----------

EulerOS 2.9 is used as the sample OS. Originally, system disk **/dev/vda** has 50 GB and one partition (**/dev/vda1**), and then 50 GB is added to the disk. In this example, the additional 50 GB is allocated to the existing **/dev/vda1** partition.

#. Expand the capacity of the system disk on the EVS console.

#. Log in to the node and run the **growpart** command to check whether growpart has been installed.

   If the tool operation guide is displayed, the tool has been installed. Otherwise, run the following command to install it:

   .. code-block::

      yum install cloud-utils-growpart

#. Run the following command to view the total capacity of the system disk **/dev/vda**:

   .. code-block::

      fdisk -l

   The following information is displayed, indicating that the total capacity of system disk **/dev/vda** is 100 GiB:

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

   The command output is as follows:

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

   Command example: (The system disk has only one partition **/dev/vda1**. Therefore, the partition number is **1**.)

   .. code-block::

      growpart /dev/vda 1

   The command output is as follows:

   .. code-block::

      CHANGED: partition=1 start=2048 old: size=104855519 end=104857567 new: size=209713119 end=209715167

#. Run the following command to extend the file system:

   .. code-block::

      resize2fs Disk partition

   Example command:

   .. code-block::

      resize2fs /dev/vda1

   The command output is as follows:

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

#. Log in to the CCE console and click the cluster. In the navigation pane, choose **Nodes**. Click **More** > **Sync Server Data** at the row containing the target node.

Node Data Disk (Dedicated for Docker)
-------------------------------------

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

Node Data Disk (Kubernetes)
---------------------------

#. Expand the capacity of the data disk on the EVS console.

#. Log in to the CCE console and click the cluster. In the navigation pane, choose **Nodes**. Click **More** > **Sync Server Data** at the row containing the target node.

#. Log in to the target node.

#. Run the following commands on the node to add the new disk capacity to the Kubernetes disk:

   .. code-block::

      pvresize /dev/sdb
      lvextend -l+100%FREE -n vgpaas/kubernetes
      resize2fs /dev/vgpaas/kubernetes
