:original_name: cce_faq_00018.html

.. _cce_faq_00018:

What Should I Do If Container Startup Fails?
============================================

Fault Locating
--------------

On the details page of a workload, if an event is displayed indicating that the container fails to be started, perform the following steps to locate the fault:

#. Log in to the node where the abnormal workload is located.

#. Check the ID of the container where the workload pod exits abnormally.

   .. code-block::

      docker ps -a | grep $podName

#. View the logs of the corresponding container.

   .. code-block::

      docker logs $containerID

   Rectify the fault of the workload based on logs.

#. Check the error logs.

   .. code-block::

      cat /var/log/messages | grep $containerID  | grep oom

   Check whether the system OOM is triggered based on the logs.

Troubleshooting Process
-----------------------

Determine the cause based on the event information, as listed in :ref:`Table 1 <cce_faq_00018__table230510269532>`.

.. _cce_faq_00018__table230510269532:

.. table:: **Table 1** Container startup failure

   +-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | Log or Event                                                                                                                                                                                                                                        | Cause and Solution                                                                                                                       |
   +=====================================================================================================================================================================================================================================================+==========================================================================================================================================+
   | The log contains **exit(0)**.                                                                                                                                                                                                                       | No process exists in the container.                                                                                                      |
   |                                                                                                                                                                                                                                                     |                                                                                                                                          |
   |                                                                                                                                                                                                                                                     | Check whether the container is running properly.                                                                                         |
   |                                                                                                                                                                                                                                                     |                                                                                                                                          |
   |                                                                                                                                                                                                                                                     | :ref:`Check Item 1: Whether There Are Processes that Keep Running in the Container (Exit Code: 0) <cce_faq_00018__section2524165018111>` |
   +-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | Event information: **Liveness probe failed: Get http...**                                                                                                                                                                                           | Health check fails.                                                                                                                      |
   |                                                                                                                                                                                                                                                     |                                                                                                                                          |
   | The log contains **exit(137)**.                                                                                                                                                                                                                     | :ref:`Check Item 2: Whether Health Check Fails to Be Performed (Exit Code: 137) <cce_faq_00018__section1766510426482>`                   |
   +-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | Event information:                                                                                                                                                                                                                                  | The disk space is insufficient. Clear the disk space.                                                                                    |
   |                                                                                                                                                                                                                                                     |                                                                                                                                          |
   | Thin Pool has 15991 free data blocks which are less than minimum required 16383 free data blocks. Create more free space in thin pool or use dm.min_free_space option to change behavior                                                            | :ref:`Check Item 3: Whether the Container Disk Space Is Insufficient <cce_faq_00018__section169421237111219>`                            |
   +-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | The keyword OOM exists in the log.                                                                                                                                                                                                                  | The memory is insufficient.                                                                                                              |
   |                                                                                                                                                                                                                                                     |                                                                                                                                          |
   |                                                                                                                                                                                                                                                     | :ref:`Check Item 4: Whether the Upper Limit of Container Resources Has Been Reached <cce_faq_00018__section060854916109>`                |
   |                                                                                                                                                                                                                                                     |                                                                                                                                          |
   |                                                                                                                                                                                                                                                     | :ref:`Check Item 5: Whether the Resource Limits Are Improperly Configured for the Container <cce_faq_00018__section1548114151414>`       |
   +-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | Address already in use                                                                                                                                                                                                                              | A conflict occurs between container ports in the pod.                                                                                    |
   |                                                                                                                                                                                                                                                     |                                                                                                                                          |
   |                                                                                                                                                                                                                                                     | :ref:`Check Item 6: Whether the Container Ports in the Same Pod Conflict with Each Other <cce_faq_00018__section17679197145618>`         |
   +-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | Error: failed to start container "filebeat": Error response from daemon: OCI runtime create failed: container_linux.go:330: starting container process caused "process_linux.go:381: container init caused \\"setenv: invalid argument\\"": unknown | A secret is mounted to the workload, and the value of the secret is not encrypted using Base64.                                          |
   |                                                                                                                                                                                                                                                     |                                                                                                                                          |
   |                                                                                                                                                                                                                                                     | :ref:`Check Item 7: Whether the Value of the Secret Mounted to the Workload Meets Requirements <cce_faq_00018__section12171141792912>`   |
   +-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+

In addition to the preceding possible causes, there are some other possible causes:

-  :ref:`Check Item 8: Whether the Container Startup Command Is Correctly Configured <cce_faq_00018__section1842111295128>`
-  :ref:`Check Item 9: Whether the User Service Has a Bug <cce_faq_00018__section16311023103717>`
-  Use the correct image when you create a workload on an Arm node.


.. figure:: /_static/images/en-us_image_0000002101595997.png
   :alt: **Figure 1** Troubleshooting process of the container restart failure

   **Figure 1** Troubleshooting process of the container restart failure

.. _cce_faq_00018__section2524165018111:

Check Item 1: Whether There Are Processes that Keep Running in the Container (Exit Code: 0)
-------------------------------------------------------------------------------------------

#. Log in to the node where the abnormal workload is located.

#. View the container status.

   .. code-block::

      docker ps -a | grep $podName

   Example:

   |image1|

   If no running process exists in the container, the status code **Exited (0)** is displayed.

.. _cce_faq_00018__section1766510426482:

Check Item 2: Whether Health Check Fails to Be Performed (Exit Code: 137)
-------------------------------------------------------------------------

The health check configured for a workload is performed on services periodically. If an exception occurs, the pod reports an event and the pod fails to be restarted.

If the liveness-type (workload liveness probe) health check is configured for the workload and the number of health check failures exceeds the threshold, the containers in the pod will be restarted. On the workload details page, if Kubernetes events contain **Liveness probe failed: Get http...**, the health check fails.

**Solution**

Click the workload name to go to the workload details page, click the **Containers** tab. Then select **Health Check** to check whether the policy is proper or whether services are running properly.

.. _cce_faq_00018__section169421237111219:

Check Item 3: Whether the Container Disk Space Is Insufficient
--------------------------------------------------------------

The following message refers to the thin pool disk that is allocated from the Docker disk selected during node creation. You can run the **lvs** command as user **root** to view the current disk usage.

.. code-block::

   Thin Pool has 15991 free data blocks which are less than minimum required 16383 free data blocks. Create more free space in thin pool or use dm.min_free_space option to change behavior

|image2|

**Solution**

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

To expand a disk capacity, perform the following steps:

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
         sda                   8:0    0   50G  0 disk
         └─sda1                8:1    0   50G  0 part /
         sdb                   8:16   0  150G  0 disk      # The data disk has been expanded to 150 GiB, but 50 GiB space is not allocated.
         ├─vgpaas-dockersys  253:0    0   90G  0 lvm  /var/lib/containerd
         └─vgpaas-kubernetes 253:1    0   10G  0 lvm  /mnt/paas/kubernetes/kubelet

   b. Expand the disk capacity.

      Add the new disk capacity to the **dockersys** logical volume used by the container engine.

      #. Expand the PV capacity so that LVM can identify the new EVS capacity. */dev/sdb* specifies the physical volume where dockersys is located.

         .. code-block::

            pvresize /dev/sdb

         Information similar to the following is displayed:

         .. code-block::

            Physical volume "/dev/sdb" changed
            1 physical volume(s) resized or updated / 0 physical volume(s) not resized

      #. Expand 100% of the free capacity to the logical volume. *vgpaas/dockersys* specifies the logical volume used by the container engine.

         .. code-block::

            lvextend -l+100%FREE -n vgpaas/dockersys

         Information similar to the following is displayed:

         .. code-block::

            Size of logical volume vgpaas/dockersys changed from <90.00 GiB (23039 extents) to 140.00 GiB (35840 extents).
            Logical volume vgpaas/dockersys successfully resized.

      #. Adjust the size of the file system. */dev/vgpaas/dockersys* specifies the file system path of the container engine.

         .. code-block::

            resize2fs /dev/vgpaas/dockersys

         Information similar to the following is displayed:

         .. code-block::

            Filesystem at /dev/vgpaas/dockersys is mounted on /var/lib/containerd; on-line resizing required
            old_desc_blocks = 12, new_desc_blocks = 18
            The filesystem on /dev/vgpaas/dockersys is now 36700160 blocks long.

   c. Check whether the capacity is expanded.

      .. code-block::

         # lsblk
         NAME                MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
         sda                   8:0    0   50G  0 disk
         └─sda1                8:1    0   50G  0 part /
         sdb                   8:16   0  150G  0 disk
         ├─vgpaas-dockersys  253:0    0   140G  0 lvm  /var/lib/containerd
         └─vgpaas-kubernetes 253:1    0   10G  0 lvm  /mnt/paas/kubernetes/kubelet

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

      #. Check whether the capacity is expanded. Run the **lsblk** command to check the disk and partition sizes of the device. If the new disk capacity has been added to the thin pool, the capacity is expanded.

         .. code-block::

            # lsblk
            NAME                                MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
            vda                                   8:0    0   50G  0 disk
            └─vda1                                8:1    0   50G  0 part /
            vdb                                   8:16   0  200G  0 disk
            ├─vgpaas-dockersys                  253:0    0   18G  0 lvm  /var/lib/docker
            ├─vgpaas-thinpool_tmeta             253:1    0    3G  0 lvm
            │ └─vgpaas-thinpool                 253:3    0   167G  0 lvm             # Thin pool space after capacity expansion
            │   ...
            ├─vgpaas-thinpool_tdata             253:2    0   67G  0 lvm
            │ └─vgpaas-thinpool                 253:3    0   67G  0 lvm
            │   ...
            └─vgpaas-kubernetes                 253:4    0   10G  0 lvm  /mnt/paas/kubernetes/kubelet

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

            Size of logical volume vgpaas/dockersys changed from <18.00 GiB (4607 extents) to <118.00 GiB (30208 extents).
            Logical volume vgpaas/dockersys successfully resized.

      #. Adjust the size of the file system. */dev/vgpaas/dockersys* specifies the file system path of the container engine.

         .. code-block::

            resize2fs /dev/vgpaas/dockersys

         Information similar to the following is displayed:

         .. code-block::

            Filesystem at /dev/vgpaas/dockersys is mounted on /var/lib/docker; on-line resizing required
            old_desc_blocks = 3, new_desc_blocks = 15
            The filesystem on /dev/vgpaas/dockersys is now 30932992 blocks long.

      #. Check whether the capacity is expanded. Run the **lsblk** command to check the disk and partition sizes of the device. If the new disk capacity has been added to the dockersys, the capacity is expanded.

         .. code-block::

            # lsblk
            NAME                                MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
            vda                                   8:0    0   50G  0 disk
            └─vda1                                8:1    0   50G  0 part /
            vdb                                   8:16   0  200G  0 disk
            ├─vgpaas-dockersys                  253:0    0   118G  0 lvm  /var/lib/docker     # dockersys after capacity expansion
            ├─vgpaas-thinpool_tmeta             253:1    0    3G  0 lvm
            │ └─vgpaas-thinpool                 253:3    0   67G  0 lvm
            │   ...
            ├─vgpaas-thinpool_tdata             253:2    0   67G  0 lvm
            │ └─vgpaas-thinpool                 253:3    0   67G  0 lvm
            │   ...
            └─vgpaas-kubernetes                 253:4    0   10G  0 lvm  /mnt/paas/kubernetes/kubelet

.. _cce_faq_00018__section060854916109:

Check Item 4: Whether the Upper Limit of Container Resources Has Been Reached
-----------------------------------------------------------------------------

If the upper limit of container resources has been reached, OOM will be displayed in the event details as well as in the log:

.. code-block::

   cat /var/log/messages | grep 96feb0a425d6 | grep oom

|image3|

When a workload is created, if the requested resources exceed the configured upper limit, the system OOM is triggered and the container exits unexpectedly.

.. _cce_faq_00018__section1548114151414:

Check Item 5: Whether the Resource Limits Are Improperly Configured for the Container
-------------------------------------------------------------------------------------

If the resource limits set for the container during workload creation are less than required, the container fails to be restarted.

.. _cce_faq_00018__section17679197145618:

Check Item 6: Whether the Container Ports in the Same Pod Conflict with Each Other
----------------------------------------------------------------------------------

#. Log in to the node where the abnormal workload is located.

#. Check the ID of the container where the workload pod exits abnormally.

   **docker ps -a \| grep** *$podName*

#. View the logs of the corresponding container.

   **docker logs** *$containerID*

   Rectify the fault of the workload based on logs. As shown in the following figure, container ports in the same pod conflict. As a result, the container fails to be started.


   .. figure:: /_static/images/en-us_image_0000002065479090.png
      :alt: **Figure 2** Container restart failure due to a container port conflict

      **Figure 2** Container restart failure due to a container port conflict

**Solution**

Re-create the workload and set a port number that is not used by any other pod.

.. _cce_faq_00018__section12171141792912:

Check Item 7: Whether the Value of the Secret Mounted to the Workload Meets Requirements
----------------------------------------------------------------------------------------

Information similar to the following is displayed in the event:

.. code-block::

   Error: failed to start container "filebeat": Error response from daemon: OCI runtime create failed: container_linux.go:330: starting container process caused "process_linux.go:381: container init caused \"setenv: invalid argument\"": unknown

The root cause is that a secret is mounted to the workload, but the value of the secret is not encrypted using Base64.

**Solution**:

Create a secret on the console. The value of the secret is automatically encrypted using Base64.

If you use YAML to create a secret, you need to manually encrypt its value using Base64.

.. code-block::

   # echo -n "Content to be encoded" | base64

.. _cce_faq_00018__section1842111295128:

Check Item 8: Whether the Container Startup Command Is Correctly Configured
---------------------------------------------------------------------------

The error messages are as follows:

|image4|

**Solution**

Click the workload name to go to the workload details page, click the **Containers** tab. Choose **Lifecycle**, click **Startup Command**, and ensure that the command is correct.

.. _cce_faq_00018__section16311023103717:

Check Item 9: Whether the User Service Has a Bug
------------------------------------------------

Check whether the workload startup command is correctly executed or whether the workload has a bug.

#. Log in to the node where the abnormal workload is located.

#. Check the ID of the container where the workload pod exits abnormally.

   .. code-block::

      docker ps -a | grep $podName

#. View the logs of the corresponding container.

   .. code-block::

      docker logs $containerID

   Note: In the preceding command, *containerID* indicates the ID of the container that has exited.


   .. figure:: /_static/images/en-us_image_0000002101677485.png
      :alt: **Figure 3** Incorrect startup command of the container

      **Figure 3** Incorrect startup command of the container

   As shown in the figure above, the container fails to be started due to an incorrect startup command. For other errors, rectify the bugs based on the logs.

**Solution**

Create a new workload and configure a correct startup command.

.. |image1| image:: /_static/images/en-us_image_0000002065637450.png
.. |image2| image:: /_static/images/en-us_image_0000002101677513.png
.. |image3| image:: /_static/images/en-us_image_0000002101595969.png
.. |image4| image:: /_static/images/en-us_image_0000002101677497.png
