:original_name: cce_faq_00224.html

.. _cce_faq_00224:

How Do I Expand the Storage Capacity of a Container?
====================================================

Application Scenarios
---------------------

The default storage size of a container is 10 GiB. If a large volume of data is generated in the container, expand the capacity using the method described in this topic.

Solution
--------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. Choose **Nodes** from the navigation pane.

#. Click the **Nodes** tab, locate the row containing the target node, and choose **More** > **Reset Node** in the **Operation** column.

   .. important::

      Resetting a node may make the node-specific resources (such as local storage and workloads scheduled to this node) unavailable. Exercise caution when performing this operation to avoid impact on running services.

#. Reconfigure node parameters.

   If you need to adjust the container storage space, pay attention to the following configurations:

   **Storage Settings**: Click **Expand** next to the data disk to set the following parameter:

   **Space Allocation for Pods**: indicates the base size of a pod. It is the maximum size that a workload's pods (including the container images) can grow to in the disk space. Proper settings can prevent pods from taking all the disk space available and avoid service exceptions. It is recommended that the value is less than or equal to 80% of the container engine space. This parameter is related to the node OS and container storage rootfs and is not supported in some scenarios. For details, see :ref:`Data Disk Space Allocation <cce_10_0341>`.

#. After the node is reset, log in to the node and check whether the container capacity has been expanded. The command output varies with the container storage rootfs.

   -  Overlayfs: No independent thin pool is allocated. Image data is stored in **dockersys**. Run the following command to check whether the container capacity has been expanded:

      **docker exec -it** *container_id* **/bin/sh** or **kubectl exec -it** *container_id* **/bin/sh**

      **df -h**

      If the information similar to the following is displayed, the overlay capacity has been expanded from 10 GiB to 15 GiB.

      .. code-block::

         Filesystem                    Size   Used   Avail   Use%   Mounted on
         overlay                        15G   104K     15G     1%   /
         tmpfs                          64M      0     64M     0%   /dev
         tmpfs                         3.6G      0    3.6G     0%   /sys/fs/cgroup
         /dev/mapper/vgpaas-share       98G   4.0G     89G     5%   /etc/hosts
         ...

   -  Devicemapper: A thin pool is allocated to store image data. Run the following command to check whether the container capacity has been expanded:

      **docker exec -it** *container_id* **/bin/sh** or **kubectl exec -it** *container_id* **/bin/sh**

      **df -h**

      If the information similar to the following is displayed, the thin pool capacity has been expanded from 10 GiB to 15 GiB.

      .. code-block::

         Filesystem                            Size   Used   Avail   Use%   Mounted on
         /dev/mapper/vgpaas-thinpool-snap-84    15G   232M     15G     2%   /
         tmpfs                                  64M      0     64M     0%   /dev
         tmpfs                                 3.6G      0    3.6G     0%   /sys/fs/cgroup
         /dev/mapper/vgpaas-kubernetes          11G    41M     11G     1%   /etc/hosts
         /dev/mapper/vgpaas-dockersys           20G   1.1G     18G     6%   /etc/hostname
         ...
