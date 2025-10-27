:original_name: cce_faq_00018.html

.. _cce_faq_00018:

What Should I Do If a Pod Startup Fails?
========================================

Fault Locating
--------------

On the details page of a workload, if an event is displayed indicating that the pod fails to be started, perform the following operations to locate the fault:

#. Log in to the node where the abnormal workload is located.

#. Check the ID of the container where the workload pod exits abnormally.

   If the node uses Docker, run the following command:

   .. code-block::

      docker ps -a | grep $podName

   If the node uses containerd, run the following command:

   .. code-block::

      crictl ps -a | grep $podName

#. View the container logs.

   If the node uses Docker, run the following command:

   .. code-block::

      docker logs $containerID

   If the node uses containerd, run the following command:

   .. code-block::

      crictl logs $containerID

   Rectify the fault of the workload based on logs.

#. Check the error logs of the OS. For example, check whether the logs contain any OOM errors.

   .. code-block::

      cat /var/log/messages | grep $containerID  | grep oom

   Check whether a system OOM is triggered based on the logs.

Troubleshooting
---------------

Determine the cause based on the logs or events, as listed in :ref:`Table 1 <cce_faq_00018__table230510269532>`.

.. _cce_faq_00018__table230510269532:

.. table:: **Table 1** Pod startup failure

   +------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Log or Event                                                                                                                                                                                                                                         | Possible Cause                                                                                  | Fault Locating and Solution                                                                                                                                              |
   +======================================================================================================================================================================================================================================================+=================================================================================================+==========================================================================================================================================================================+
   | Pod logs: exit code 0                                                                                                                                                                                                                                | There is no process in the pod.                                                                 | Check whether the pod can run properly. For details, see :ref:`No Process in the Pod (Exit Code: 0) <cce_faq_00018__section2524165018111>`.                              |
   +------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | -  Kubernetes event:                                                                                                                                                                                                                                 | The health check fails.                                                                         | Check whether the liveness probe for the pod is properly configured. For details, see :ref:`Health Check Failed (Exit Code: 137) <cce_faq_00018__section1766510426482>`. |
   |                                                                                                                                                                                                                                                      |                                                                                                 |                                                                                                                                                                          |
   |    .. code-block::                                                                                                                                                                                                                                   |                                                                                                 |                                                                                                                                                                          |
   |                                                                                                                                                                                                                                                      |                                                                                                 |                                                                                                                                                                          |
   |       Liveness probe failed: Get http…                                                                                                                                                                                                               |                                                                                                 |                                                                                                                                                                          |
   |                                                                                                                                                                                                                                                      |                                                                                                 |                                                                                                                                                                          |
   | -  Pod logs: exit code 137                                                                                                                                                                                                                           |                                                                                                 |                                                                                                                                                                          |
   +------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | -  Kubernetes event:                                                                                                                                                                                                                                 | The disk space is insufficient.                                                                 | Expand the disk space or clear unneeded files. For details, see :ref:`Insufficient Disk Space of the Pod <cce_faq_00018__section169421237111219>`.                       |
   |                                                                                                                                                                                                                                                      |                                                                                                 |                                                                                                                                                                          |
   |    .. code-block::                                                                                                                                                                                                                                   |                                                                                                 |                                                                                                                                                                          |
   |                                                                                                                                                                                                                                                      |                                                                                                 |                                                                                                                                                                          |
   |       Thin Pool has 15991 free data blocks which is less than minimum required 16383 free data blocks. Create more free space in thin pool or use dm.min_free_space option to change behavior                                                        |                                                                                                 |                                                                                                                                                                          |
   |                                                                                                                                                                                                                                                      |                                                                                                 |                                                                                                                                                                          |
   | -  Pod logs: **no left space**                                                                                                                                                                                                                       |                                                                                                 |                                                                                                                                                                          |
   +------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Pod logs: **oom**                                                                                                                                                                                                                                    | The pod memory is insufficient.                                                                 | Check whether the pod has proper resource settings. For details, see :ref:`Insufficient Container Resources <cce_faq_00018__section060854916109>`.                       |
   +------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Pod logs: **Address already in use**                                                                                                                                                                                                                 | A conflict occurs between container ports in the pod.                                           | Check whether there is a container port conflict in the pod. For details, see :ref:`Container Port Conflict in the Pod <cce_faq_00018__section17679197145618>`.          |
   +------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Kubernetes event:                                                                                                                                                                                                                                    | A secret is mounted to the workload, and the value of the secret is not encrypted using Base64. | For details about the solution, see :ref:`Improper Value of the Secret Mounted to the Workload <cce_faq_00018__section12171141792912>`.                                  |
   |                                                                                                                                                                                                                                                      |                                                                                                 |                                                                                                                                                                          |
   | .. code-block::                                                                                                                                                                                                                                      |                                                                                                 |                                                                                                                                                                          |
   |                                                                                                                                                                                                                                                      |                                                                                                 |                                                                                                                                                                          |
   |    Error: failed to start container "filebeat": Error response from daemon: OCI runtime create failed: container_linux.go:330: starting container process caused "process_linux.go:381: container init caused \"setenv: invalid argument\"": unknown |                                                                                                 |                                                                                                                                                                          |
   +------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Kubernetes event:                                                                                                                                                                                                                                    | The x86 container image may run on an Arm node.                                                 | For details about the solution, see :ref:`Unmatched Container Image Tag with the Node Architecture <cce_faq_00018__section1141244791317>`.                               |
   |                                                                                                                                                                                                                                                      |                                                                                                 |                                                                                                                                                                          |
   | .. code-block::                                                                                                                                                                                                                                      |                                                                                                 |                                                                                                                                                                          |
   |                                                                                                                                                                                                                                                      |                                                                                                 |                                                                                                                                                                          |
   |    the failed container exited with ExitCode: 255                                                                                                                                                                                                    |                                                                                                 |                                                                                                                                                                          |
   +------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Kubernetes event:                                                                                                                                                                                                                                    | The containerd version is incompatible with the tail version.                                   | For details about the solution, see :ref:`Exit of tail -f xx in the Container Startup Command (Exit Code: 141) <cce_faq_00018__section13939123335611>`.                  |
   |                                                                                                                                                                                                                                                      |                                                                                                 |                                                                                                                                                                          |
   | .. code-block::                                                                                                                                                                                                                                      |                                                                                                 |                                                                                                                                                                          |
   |                                                                                                                                                                                                                                                      |                                                                                                 |                                                                                                                                                                          |
   |    the failed container exited with ExitCode: 141                                                                                                                                                                                                    |                                                                                                 |                                                                                                                                                                          |
   +------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Other pod logs                                                                                                                                                                                                                                       | Locate the fault based on services.                                                             | For details about the fault locating, see :ref:`Service Setting Checks <cce_faq_00018__section16311023103717>`.                                                          |
   +------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_faq_00018__section2524165018111:

No Process in the Pod (Exit Code: 0)
------------------------------------

#. Log in to the node where the abnormal workload is located.

#. View the pod status.

   If the node uses Docker, run the following command:

   .. code-block::

      docker ps -a | grep $podName

   If the node uses containerd, run the following command:

   .. code-block::

      crictl ps -a | grep $podName

   Below shows an example.

   |image1|

   If there is no process in the pod, the status code **Exited (0)** is displayed.

.. _cce_faq_00018__section1766510426482:

Health Check Failed (Exit Code: 137)
------------------------------------

The health checks configured for a workload are performed on services periodically. If an exception occurs, there will be an event that indicates an unhealthy pod, and the pod restarts will fail.

If a liveness probe is configured for the workload and the number of health check failures exceeds the threshold, the pod will be restarted. On the workload details page, if Kubernetes events contain **Liveness probe failed: Get http...**, the health check fails.

**Solution**

Click the workload name to go to the workload details page, click the **Containers** tab. Then select **Health Check** to check whether the policy is proper or whether services are running properly.

.. _cce_faq_00018__section169421237111219:

Insufficient Disk Space of the Pod
----------------------------------

The following message refers to the thin pool disk that is allocated from the Docker disk selected during node creation. You can run the **lvs** command as user **root** to view the current disk usage.

.. code-block::

   Thin Pool has 15991 free data blocks which is less than minimum required 16383 free data blocks. Create more free space in thin pool or use dm.min_free_space option to change behavior

|image2|

**Solution**

**Solution 1: Clearing images**

Perform the following operations to clear unused images:

-  Nodes that use containerd

   #. Obtain local images on the node.

      .. code-block::

         crictl images -v

   #. Delete the unnecessary images by image ID.

      .. code-block::

         crictl rmi {Image ID}

-  Nodes that use Docker

   #. Obtain local images on the node.

      .. code-block::

         docker images

   #. Delete the unnecessary images by image ID.

      .. code-block::

         docker rmi {}Image ID}

.. note::

   Do not delete system images such as the **cce-pause** image. Otherwise, the pod creation may fail.

**Solution 2: Expanding the disk capacity**

To expand a disk capacity, perform the following operations:

#. Expand the capacity of a data disk on the EVS console.

   Only the storage capacity of EVS disks can be expanded. You need to perform the following operations to expand the capacity of logical volumes and file systems.

#. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Nodes**. In the right pane, click the **Nodes** tab, locate the row containing the target node, and choose **More** > **Sync Server Data** in the **Operation** column.

#. Log in to the target node.

#. Run **lsblk** to view the block device information of the node.

   A data disk is divided depending on the container storage **Rootfs**:

   Overlayfs: No independent thin pool is allocated. Image data is stored in **dockersys**.

   a. Check the disk and partition space of the device.

      .. code-block::

         # lsblk
         NAME                MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
         sda                   8:0    0   50G  0 disk
         └─sda1                8:1    0   50G  0 part /
         sdb                   8:16   0  150G  0 disk      # The data disk has been expanded to 150 GiB, but 50 GiB space is free.
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

   c. Check whether the capacity has been expanded.

      .. code-block::

         # lsblk
         NAME                MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
         sda                   8:0    0   50G  0 disk
         └─sda1                8:1    0   50G  0 part /
         sdb                   8:16   0  150G  0 disk
         ├─vgpaas-dockersys  253:0    0   140G  0 lvm  /var/lib/containerd
         └─vgpaas-kubernetes 253:1    0   10G  0 lvm  /mnt/paas/kubernetes/kubelet

   Device Mapper: A thin pool is allocated to store image data.

   a. Check the disk and partition space of the device.

      .. code-block::

         # lsblk
         NAME                                MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
         vda                                   8:0    0   50G  0 disk
         └─vda1                                8:1    0   50G  0 part /
         vdb                                   8:16   0  200G  0 disk
         ├─vgpaas-dockersys                  253:0    0   18G  0 lvm  /var/lib/docker
         ├─vgpaas-thinpool_tmeta             253:1    0    3G  0 lvm
         │ └─vgpaas-thinpool                 253:3    0   67G  0 lvm                   # Space used by thin pool
         │   ...
         ├─vgpaas-thinpool_tdata             253:2    0   67G  0 lvm
         │ └─vgpaas-thinpool                 253:3    0   67G  0 lvm
         │   ...
         └─vgpaas-kubernetes                 253:4    0   10G  0 lvm  /mnt/paas/kubernetes/kubelet

   b. Expand the disk capacity.

      Option 1: Add the new disk capacity to the thin pool.

      #. Expand the PV capacity so that LVM can identify the new EVS capacity. */dev/vdb* specifies the physical volume where thin pool is located.

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

      #. Run the **lsblk** command to check the disk and partition space of the device and check whether the capacity has been expanded. If the new disk capacity was added to the thin pool, the capacity has been expanded.

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

      #. Run the **lsblk** command to check the disk and partition space of the device and check whether the capacity has been expanded. If the new disk capacity was added to the dockersys, the capacity has been expanded.

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

Insufficient Container Resources
--------------------------------

If the upper limit of container resources has been reached, OOM will be displayed in the event details as well as in the log:

.. code-block::

   cat /var/log/messages | grep 96feb0a425d6 | grep oom

|image3|

When a workload is created, if the requested resources exceed the configured upper limit, the system OOM is triggered and the container exits unexpectedly.

.. _cce_faq_00018__section17679197145618:

Container Port Conflict in the Pod
----------------------------------

#. Log in to the node where the abnormal workload is located.

#. Check the ID of the container where the workload pod exits abnormally.

   If the node uses Docker, run the following command:

   .. code-block::

      docker ps -a | grep $podName

   If the node uses containerd, run the following command:

   .. code-block::

      crictl ps -a | grep $podName

#. View the container logs.

   If the node uses Docker, run the following command:

   .. code-block::

      docker logs $containerID

   If the node uses containerd, run the following command:

   .. code-block::

      crictl logs $containerID

   Rectify the fault of the workload based on logs. As shown in the following figure, container ports in the same pod conflict. As a result, the container fails to be started.


   .. figure:: /_static/images/en-us_image_0000002467677145.png
      :alt: **Figure 1** Pod restart failure due to a container port conflict

      **Figure 1** Pod restart failure due to a container port conflict

**Solution**

Configure proper container ports that do not conflict with each other. Then, create the workload again.

If a pod uses the host network (with **hostNetwork: true** configured), there may be a container port conflict. This is because containers in the pod share the network interface and port range with the host node. Multiple pods using the same port cannot run on the same node.

.. _cce_faq_00018__section12171141792912:

Improper Value of the Secret Mounted to the Workload
----------------------------------------------------

Information similar to the following is displayed in the event:

.. code-block::

   Error: failed to start container "filebeat": Error response from daemon: OCI runtime create failed: container_linux.go:330: starting container process caused "process_linux.go:381: container init caused \"setenv: invalid argument\"": unknown

The root cause is that a secret is mounted to the workload, but the value of the secret is not encrypted using Base64.

**Solution**

Create a secret on the console. The value of the secret is automatically encrypted using Base64.

If you use YAML to create a secret, you need to manually encrypt its value using Base64.

.. code-block::

   echo -n "Content to be encoded" | base64

.. _cce_faq_00018__section1141244791317:

Unmatched Container Image Tag with the Node Architecture
--------------------------------------------------------

The proper image tag is not used during the workload creation on an Arm node. To resolve this issue, use the proper image tag.

.. _cce_faq_00018__section13939123335611:

Exit of **tail -f** *xx* in the Container Startup Command (Exit Code: 141)
--------------------------------------------------------------------------

The Kubernetes event is as follows:

.. code-block::

   the failed container exited with ExitCode: 141

**Possible Cause**

The legacy version of containerd is incompatible with the tail version (>= 8.28) in the container image. As a result, executing the **tail -f** command leads to an unexpected exit, returning exit code 141.

**Temporary Workaround**

Change **tail -f** *xx* in the startup parameters to **sleep 2 && tail -f** *xx* and create a workload again.

**Solution**

-  Upgrade the cluster to v1.25.16-r20, v1.27.16-r20, v1.28.15-r10, v1.29.10-r10, v1.30.6-r10, v1.31.4-r0, or later.
-  Reset the node and use the Docker container engine instead.

.. _cce_faq_00018__section16311023103717:

Service Setting Checks
----------------------

Check whether the workload startup command is correctly executed or whether the workload has a bug.

#. Log in to the node where the abnormal workload is located.

#. Check the ID of the container where the workload pod exits abnormally.

   If the node uses Docker, run the following command:

   .. code-block::

      docker ps -a | grep $podName

   If the node uses containerd, run the following command:

   .. code-block::

      crictl ps -a | grep $podName

#. View the container logs.

   If the node uses Docker, run the following command:

   .. code-block::

      docker logs $containerID

   If the node uses containerd, run the following command:

   .. code-block::

      crictl logs $containerID

   Note: In the preceding command, *containerID* indicates the ID of the container that has exited.


   .. figure:: /_static/images/en-us_image_0000002467717277.png
      :alt: **Figure 2** Incorrect startup command of the container

      **Figure 2** Incorrect startup command of the container

   As shown in the figure above, the container fails to be started due to an incorrect startup command. For other errors, rectify the bugs based on the logs.

**Solution**

Create a new workload and configure a correct startup command.

.. |image1| image:: /_static/images/en-us_image_0000002467677157.png
.. |image2| image:: /_static/images/en-us_image_0000002434238660.png
.. |image3| image:: /_static/images/en-us_image_0000002434238636.png
