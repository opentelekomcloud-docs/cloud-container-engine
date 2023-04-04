:original_name: cce_bestpractice_00198.html

.. _cce_bestpractice_00198:

Expanding Node Disk Capacity
============================

System Disk
-----------

#. Expand the capacity of the system disk on the EVS console.
#. Restart the node on the ECS console.
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

      Run the following commands on the node to add the new disk capacity to the **thinpool** disk:

      .. code-block::

         pvresize /dev/sdb
         lvextend -l+100%FREE -n vgpaas/thinpool

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
