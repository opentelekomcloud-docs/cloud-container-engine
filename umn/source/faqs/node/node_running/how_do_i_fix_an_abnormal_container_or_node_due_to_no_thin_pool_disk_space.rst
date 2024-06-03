:original_name: cce_faq_00307.html

.. _cce_faq_00307:

How Do I Fix an Abnormal Container or Node Due to No Thin Pool Disk Space?
==========================================================================

Problem Description
-------------------

When the disk space of a thin pool on a node is about to be used up, the following exceptions occasionally occur:

Files or directories fail to be created in the container, the file system in the container is read-only, the node is tainted disk-pressure, or the node is unavailable.

You can run the **docker info** command on the node to view the used and remaining thin pool space to locate the fault. The following figure is an example.

|image1|

Possible Cause
--------------

When Docker device mapper is used, although you can configure the **basesize** parameter to limit the size of the **/home** directory of a single container (to 10 GB by default), all containers on the node still share the thin pool of the node for storage. They are not completely isolated. When the sum of the thin pool space used by certain containers reaches the upper limit, other containers cannot run properly.

In addition, after a file is deleted in the **/home** directory of the container, the thin pool space occupied by the file is not released immediately. Therefore, even if **basesize** is set to 10 GB, the thin pool space occupied by files keeps increasing until 10 GB when files are created in the container. The space released after file deletion will be reused only after a while. If **the number of service containers on the node multiplied by basesize** is greater than the thin pool space size of the node, there is a possibility that the thin pool space has been used up.

Solution
--------

When the thin pool space of a node is used up, some services can be migrated to other nodes to quickly recover services. But you are advised to use the following solutions to resolve the root cause:

**Solution 1:**

Properly plan the service distribution and data plane disk space to avoid the scenario where **the number of service containers multiplied by basesize** is greater than the thin pool size of the node. To expand the thin pool size, perform the following steps:

#. Expand the capacity of the data disk on the EVS console.

#. Log in to the CCE console and click the cluster. In the navigation pane, choose **Nodes**. Click **More** > **Sync Server Data** in the row containing the target node.

#. Log in to the target node.

#. Run the **lsblk** command to check the block device information of the node.

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

**Solution 2:**

Create and delete files in service containers in the local storage (such as emptyDir and hostPath) or cloud storage directory mounted to the container. Such files do not occupy the thin pool space.

**Solution 3:**

If the OS uses OverlayFS, services can be deployed on such nodes to prevent the problem that the disk space occupied by files created or deleted in the container is not released immediately.

.. |image1| image:: /_static/images/en-us_image_0000001851743812.png
