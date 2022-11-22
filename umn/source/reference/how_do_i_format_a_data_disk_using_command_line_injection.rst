:original_name: cce_01_0204.html

.. _cce_01_0204:

How Do I Format a Data Disk Using Command Line Injection?
=========================================================

Before using command line injection, write a script that can format data disks and save it to your OBS bucket. Then, inject a command line that will automatically execute the disk formatting script when the node is up. Use input parameters to specify the size of each docker data disk (for example, the default docker disk of 100 GB and the additional disk of 110 GB) and the mount path (**/data/code**) of the additional disk. In this example, the script is named **formatdisk.sh**.

Example command line:

.. code-block::

   cd /tmp;curl -k -X GET OBS bucket address/formatdisk.sh -1 -O;fdisk -l;sleep 30;bash -x formatdisk.sh 100 /data/code;fdisk -l

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
       isRawDisk=$(sudo lsblk -n $device 2>/dev/null | grep disk | wc -l)
       if [[ ${isRawDisk} > 0 ]]; then
           # is it partitioned ?
           match=$(sudo lsblk -n $device 2>/dev/null | grep -v disk | wc -l)
           if [[ ${match} > 0 ]]; then
               # already partited
               [[ -n "${DOCKER_BLOCK_DEVICES}" ]] && echo "Raw disk ${device} has been partition, will skip this device"
               continue
           fi
       else
           isPart=$(sudo lsblk -n $device 2>/dev/null | grep part | wc -l)
           if [[ ${isPart} -ne 1 ]]; then
               # not parted
               [[ -n "${DOCKER_BLOCK_DEVICES}" ]] && echo "Disk ${device} has not been partition, will skip this device"
               continue
           fi
           # is used ?
           match=$(sudo lsblk -n $device 2>/dev/null | grep -v part | wc -l)
           if [[ ${match} > 0 ]]; then
               # already used
               [[ -n "${DOCKER_BLOCK_DEVICES}" ]] && echo "Disk ${device} has been used, will skip this device"
               continue
           fi
           isMount=$(sudo lsblk -n -o MOUNTPOINT $device 2>/dev/null)
           if [[ -n ${isMount} ]]; then
               # already used
               [[ -n "${DOCKER_BLOCK_DEVICES}" ]] && echo "Disk ${device} has been used, will skip this device"
               continue
           fi
           isLvm=$(sudo sfdisk -lqL 2>>/dev/null | grep $device | grep "8e.*Linux LVM")
           if [[ ! -n ${isLvm} ]]; then
               # part system type is not Linux LVM
               [[ -n "${DOCKER_BLOCK_DEVICES}" ]] && echo "Disk ${device} system type is not Linux LVM, will skip this device"
               continue
           fi
       fi
       block_devices_size=$(sudo lsblk -n -o SIZE $device 2>/dev/null | awk '{ print $1}')
       if [[ ${block_devices_size}"x" != "${dockerdisksize}Gx" ]] && [[ ${block_devices_size}"x" != "${systemdisksize}Gx" ]]; then
   echo "n
   p
   1


   w
   " | fdisk $device
           mkfs -t ext4 ${device}1
           mkdir -p $mountdir
           echo "${device}1  $mountdir ext4  noatime  0 0" | sudo tee -a /etc/fstab >/dev/null
           mount $mountdir
       fi
   done
