:original_name: cce_01_0200.html

.. _cce_01_0200:

Creating a Linux LVM Disk Partition for Docker
==============================================

Scenario
--------

This section describes how to check whether there are **available raw disks** and **Linux LVM disk partitions** and how to create Linux LVM disk partitions.

Prerequisites
-------------

To improve the system stability, attach a data disk to Docker and use the direct-lvm mode.

Procedure
---------

#. .. _cce_01_0200__li139011015111020:

   Check whether available raw disks exist on the current node.

   a. Log in to the target node as the **root** user.

   b. Check the raw disk device.

      **lsblk -l \| grep disk**

      If the following information is displayed, the raw disks named **xvda** and **xvdb** exist on the node.

      .. code-block::

         xvda  202:0    0   40G  0 disk
         xvdb  202:16   0  100G  0 disk

   c. Check whether the raw disk is in use.

      **lsblk /dev/**\ *<devicename>*

      *devicename* indicates the raw disk name, for example, **xvda** and **xvdb** in the previous step.

      Run the **lsblk /dev/xvda** and **lsblk /dev/xvdb** commands. If the following information is displayed, **xvda** has been partitioned and used while **xvdb** is available. If no raw disk is available, bind an EVS disk to the node. It is advised that the disk space be no less than 80 GB.

      .. code-block::

         NAME    MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
         xvda    202:0    0   40G  0 disk
         ├─xvda1 202:1    0  100M  0 part /boot
         └─xvda2 202:2    0 39.9G  0 part /

      .. code-block::

         NAME MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
         xvdb 202:16   0  100G  0 disk

#. Check whether there are partitions available. Currently, only Linux LVM partitions are supported.

   a. Log in to the target node as the **root** user.

   b. Check the partition whose system type is Linux LVM.

      **sfdisk -l 2>>/dev/null\| grep "Linux LVM"**

      If the following information is displayed, two Linux LVM partitions, **/dev/nvme0n1p1** and **/dev/nvme0n1p2**, exist in the system.

      .. code-block::

         /dev/nvme0n1p1          1  204800  204800  209715200   8e  Linux LVM
         /dev/nvme0n1p2     204801  409600  204800  209715200   8e  Linux LVM

   c. Check whether the partition is in use.

      **lsblk** *<partdevice>*

      *<partdevice>* is the Linux LVM partition found in the previous step.

      In this example, run the **lsblk/dev/nvme0n1p1** and **lsblk/dev/nvme0n1p2** commands. If the following information is displayed, partition **nvme0n1p** is in use while **nvme0n1p2** is available.

      .. code-block::

         NAME                       MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
         nvme0n1p1                   259:3    0  200G  0 part
         └─vgpaas-thinpool_tdata   251:8    0  360G  0 lvm
           └─vgpaas-thinpool       251:10   0  360G  0 lvm

      .. code-block::

         NAME      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
         nvme0n1p2 259:1    0  100G  0 part

      If no AZ is available, perform :ref:`3 <cce_01_0200__li111391316141612>` to create a partition for Docker.

#. .. _cce_01_0200__li111391316141612:

   Create a Linux LVM disk partition for Docker.

   a. Run the following command to create a disk partition. **devicename** indicates the available raw disk name, for example, **xvdb** in :ref:`1 <cce_01_0200__li139011015111020>`.

      **fdisk /dev/**\ *devicename*

   b. Enter **n** to create a new partition. Enter **p** to display the primary partition number. Enter **4** to indicate the fourth primary partition.


      .. figure:: /_static/images/en-us_image_0144042759.png
         :alt: **Figure 1** Creating a partition

         **Figure 1** Creating a partition

   c. Configure the start and last sectors as follows for example:

      .. code-block::

         Start sector (1048578048-4294967295, 1048578048 by default):
         1048578048
         Last sector, +sector or size {K, M, or G} (1048578048-4294967294, 4294967294 by default): +100G

      This configuration indicates that partition 4 has been set to the Linux type and the size is 100 GiB.

   d. Enter **t** to change the partition system type. Enter the hex code **8e** when prompted to change the system type to Linux LVM.

      .. code-block::

         Command (enter m to obtain help): t
         Partition ID (ranging from 1 to 4, 4 by default): 4
         Hex code (enter L to list all codes): 8e
         This configuration changes the type of the partition Linux to Linux LVM.

   e. Enter **w** to save the modification.

      .. code-block::

         Command (enter m to obtain help): w
         The partition table has been altered!

   f. Run the **partprobe** command to refresh the disk partition.
