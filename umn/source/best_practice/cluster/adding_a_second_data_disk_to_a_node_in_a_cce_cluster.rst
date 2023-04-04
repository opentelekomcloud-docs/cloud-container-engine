:original_name: cce_bestpractice_00190.html

.. _cce_bestpractice_00190:

Adding a Second Data Disk to a Node in a CCE Cluster
====================================================

You can use the pre-installation script feature to configure CCE cluster nodes (ECSs).

.. note::

   -  When creating a node in a cluster of v1.13.10 or later, if a data disk is not managed by LVM, follow instructions in this section to format the data disk before adding the disk. Otherwise, the data disk will still be managed by LVM.
   -  When creating a node in a cluster earlier than v1.13.10, you must format the data disks that are not managed by LVM. Otherwise, either these data disks or the first data disk will be managed by LVM.

Before using this feature, write a script that can format data disks and save it to your OBS bucket. This script must be executed by user **root**.

**Input Parameters**

#. Set the script name to **formatdisk.sh**, save the script to your OBS bucket, and obtain the address of the script in OBS.
#. You need to specify the size of the Docker data disk (the data disk managed by LVM is called the Docker data disk). The size of the Docker disk must be different from that of the second disk. For example, the Docker data disk is 100 GB and the new disk is 110 GB.
#. Set the mount path of the second data disk, for example, **/data/code**.

Run the following command in the pre-installation script to format the disk:

.. code-block::

   cd /tmp;curl -k -X GET OBS bucket address /formatdisk.sh -1 -O;fdisk -l;sleep 30;bash -x formatdisk.sh 100 /data/code;fdisk -l

Example script (**formatdisk.sh**):

.. code-block::

   dockerdisksize=$1
   mountdir=$2
   systemdisksize=40
   i=0
   while [ 20 -gt $i ]; do
       echo $i;
       if [ $(lsblk -o KNAME,TYPE | grep disk | grep -v nvme | awk '{print $1}' | awk '{ print "/dev/"$1}' |wc -l) -ge 3 ]; then
           break
       else
           sleep 5
       fi;
       i=$[i+1]
   done
   all_devices=$(lsblk -o KNAME,TYPE | grep disk | grep -v nvme | awk '{print $1}' | awk '{ print "/dev/"$1}')
   for device in ${all_devices[@]}; do
       isRawDisk=$(lsblk -n $device 2>/dev/null | grep disk | wc -l)
       if [[ ${isRawDisk} > 0 ]]; then
           # is it partitioned ?
           match=$(lsblk -n $device 2>/dev/null | grep -v disk | wc -l)
           if [[ ${match} > 0 ]]; then
               # already partited
               [[ -n "${DOCKER_BLOCK_DEVICES}" ]] && echo "Raw disk ${device} has been partition, will skip this device"
               continue
           fi
       else
           isPart=$(lsblk -n $device 2>/dev/null | grep part | wc -l)
           if [[ ${isPart} -ne 1 ]]; then
               # not parted
               [[ -n "${DOCKER_BLOCK_DEVICES}" ]] && echo "Disk ${device} has not been partition, will skip this device"
               continue
           fi
           # is used ?
           match=$(lsblk -n $device 2>/dev/null | grep -v part | wc -l)
           if [[ ${match} > 0 ]]; then
               # already used
               [[ -n "${DOCKER_BLOCK_DEVICES}" ]] && echo "Disk ${device} has been used, will skip this device"
               continue
           fi
           isMount=$(lsblk -n -o MOUNTPOINT $device 2>/dev/null)
           if [[ -n ${isMount} ]]; then
               # already used
               [[ -n "${DOCKER_BLOCK_DEVICES}" ]] && echo "Disk ${device} has been used, will skip this device"
               continue
           fi
           isLvm=$(sfdisk -lqL 2>>/dev/null | grep $device | grep "8e.*Linux LVM")
           if [[ ! -n ${isLvm} ]]; then
               # part system type is not Linux LVM
               [[ -n "${DOCKER_BLOCK_DEVICES}" ]] && echo "Disk ${device} system type is not Linux LVM, will skip this device"
               continue
           fi
       fi
       block_devices_size=$(lsblk -n -o SIZE $device 2>/dev/null | awk '{ print $1}')
       if [[ ${block_devices_size}"x" != "${dockerdisksize}Gx" ]] && [[ ${block_devices_size}"x" != "${systemdisksize}Gx" ]]; then
   echo "n
   p
   1


   w
   " | fdisk $device
           mkfs -t ext4 ${device}1
           mkdir -p $mountdir
       uuid=$(blkid ${device}1 |awk '{print $2}')
       echo "${uuid}  $mountdir ext4  noatime  0 0" | tee -a /etc/fstab >/dev/null
           mount $mountdir
       fi
   done

.. note::

   If the preceding example cannot be executed, use the dos2unix tool to convert the format.
