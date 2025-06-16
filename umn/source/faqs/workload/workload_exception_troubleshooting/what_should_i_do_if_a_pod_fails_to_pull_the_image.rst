:original_name: cce_faq_00015.html

.. _cce_faq_00015:

What Should I Do If a Pod Fails to Pull the Image?
==================================================

Fault Locating
--------------

When a workload's status shows "Pod not ready: Back-off pulling image "*xxxxx*", a Kubernetes event of **PodsFailed to pull image** or **Failed to re-pull image** will be reported. For details about how to view Kubernetes events, see :ref:`Viewing Pod Events <cce_faq_00134__section13566155892120>`.

Troubleshooting
---------------

Determine the cause based on the events, as listed in :ref:`Table 1 <cce_faq_00015__table230510269532>`.

.. _cce_faq_00015__table230510269532:

.. table:: **Table 1** Events related to an image pull failure

   +---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | Event                                                                                                                                                                                                                                                                           | Cause and Solution                                                                                                                       |
   +=================================================================================================================================================================================================================================================================================+==========================================================================================================================================+
   | Failed to pull image "xxx": rpc error: code = Unknown desc = Error response from daemon: Get xxx: denied: You may not login yet                                                                                                                                                 | You have not logged in to the image repository.                                                                                          |
   |                                                                                                                                                                                                                                                                                 |                                                                                                                                          |
   |                                                                                                                                                                                                                                                                                 | :ref:`Check Item 1: Whether imagePullSecret Is Specified When You Use kubectl to Create a Workload <cce_faq_00015__section629791052512>` |
   +---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | Failed to pull image "nginx:v1.1": rpc error: code = Unknown desc = Error response from daemon: Get https://registry-1.docker.io/v2/: dial tcp: lookup registry-1.docker.io: no such host                                                                                       | The image address is incorrectly configured.                                                                                             |
   |                                                                                                                                                                                                                                                                                 |                                                                                                                                          |
   |                                                                                                                                                                                                                                                                                 | :ref:`Check Item 2: Whether the Image Address Is Correct When a Third-Party Image Is Used <cce_faq_00015__section819316261313>`          |
   |                                                                                                                                                                                                                                                                                 |                                                                                                                                          |
   |                                                                                                                                                                                                                                                                                 | :ref:`Check Item 3: Whether an Incorrect Secret Is Used When a Third-Party Image Is Used <cce_faq_00015__section9312113135616>`          |
   +---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | Failed create pod sandbox: rpc error: code = Unknown desc = failed to create a sandbox for pod "nginx-6dc48bf8b6-l8xrw": Error response from daemon: mkdir xxxxx: no space left on device                                                                                       | The disk space is insufficient.                                                                                                          |
   |                                                                                                                                                                                                                                                                                 |                                                                                                                                          |
   |                                                                                                                                                                                                                                                                                 | :ref:`Check Item 4: Whether the Node Disk Space Is Insufficient <cce_faq_00015__section165209286116>`                                    |
   +---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | Failed to pull image "*xxx*": rpc error: code = Unknown desc = error pulling image configuration: *xxx* x509: certificate signed by unknown authority                                                                                                                           | An unknown or insecure certificate is used by the third-party image repository from which the image is pulled.                           |
   |                                                                                                                                                                                                                                                                                 |                                                                                                                                          |
   |                                                                                                                                                                                                                                                                                 | :ref:`Check Item 5: Whether the Remote Image Repository Uses an Unknown or Insecure Certificate <cce_faq_00015__section1026851817369>`   |
   +---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | Failed to pull image "*xxx*": rpc error: code = Unknown desc = context canceled                                                                                                                                                                                                 | The image size is too large.                                                                                                             |
   |                                                                                                                                                                                                                                                                                 |                                                                                                                                          |
   |                                                                                                                                                                                                                                                                                 | :ref:`Check Item 6: Whether the Image Size Is Too Large <cce_faq_00015__section1962218412226>`                                           |
   +---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | Failed to pull image "docker.io/bitnami/nginx:1.22.0-debian-11-r3": rpc error: code = Unknown desc = Error response from daemon: Get https://registry-1.docker.io/v2/: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers) | :ref:`Check Item 7: Whether the Image Repository Can Be Accessed <cce_faq_00015__section19758182810816>`                                 |
   +---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | ERROR: toomanyrequests: Too Many Requests.                                                                                                                                                                                                                                      | The rate is limited because the number of image pull times reaches the upper limit.                                                      |
   |                                                                                                                                                                                                                                                                                 |                                                                                                                                          |
   | Or                                                                                                                                                                                                                                                                              | :ref:`Check Item 8: Whether the Number of Public Image Pulls Reaches the Upper Limit <cce_faq_00015__section13904181735115>`             |
   |                                                                                                                                                                                                                                                                                 |                                                                                                                                          |
   | you have reached your pull rate limit, you may increase the limit by authenticating an upgrading                                                                                                                                                                                |                                                                                                                                          |
   +---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_faq_00015__section629791052512:

Check Item 1: Whether **imagePullSecret** Is Specified When You Use kubectl to Create a Workload
------------------------------------------------------------------------------------------------

If the workload is abnormal and a Kubernetes event that indicates the pod fails to pull the image is displayed, you can check whether the **imagePullSecrets** field exists in the YAML file.

**Items to Check**

-  If an image needs to be pulled from SWR, the **name** parameter must be set to **default-secret**.

   .. code-block::

      apiVersion: extensions/v1beta1
      kind: Deployment
      metadata:
        name: nginx
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: nginx
        strategy:
          type: RollingUpdate
        template:
          metadata:
            labels:
              app: nginx
          spec:
            containers:
            - image: nginx
              imagePullPolicy: Always
              name: nginx
            imagePullSecrets:
            - name: default-secret

-  If an image needs to be pulled from a third-party image repository, the **name** parameter must be set to the created secret name.

   When you use kubectl to create a workload from a third-party image, specify the **imagePullSecret** field, in which **name** indicates the name of the secret used to pull the image.

.. _cce_faq_00015__section819316261313:

Check Item 2: Whether the Image Address Is Correct When a Third-Party Image Is Used
-----------------------------------------------------------------------------------

CCE allows you to create workloads using images pulled from third-party image repositories.

You need to enter the third-party image addresses based on specific requirements. The image addresses must be in the format of *ip:port/path/name:version* or *name:version*. If no version is specified, **latest** is used by default.

-  For a private repository, enter an image address in the format of *ip:port/path/name:version*.
-  For an open source Docker repository, enter an image address in the format of *name:version*, for example, **nginx:latest**.

When you fail to pull an image due to incorrect image address provided, information similar to the following information is displayed in the Kubernetes events:

.. code-block::

   Failed to pull image "nginx:v1.1": rpc error: code = Unknown desc = Error response from daemon: Get https://registry-1.docker.io/v2/: dial tcp: lookup registry-1.docker.io: no such host

**Solution**

Change the image address by editing your YAML file or log in to the CCE console and replace the image on the **Upgrade** tab on the workload details page.

.. _cce_faq_00015__section9312113135616:

Check Item 3: Whether an Incorrect Secret Is Used When a Third-Party Image Is Used
----------------------------------------------------------------------------------

Typically, a third-party image repository can be accessed only after authentication (using your account and password). CCE uses the secret authentication mode to pull images, so you need to create a secret for an image repository before pulling images from the repository.

**Solution**

If your secret is incorrect, images will fail to be pulled. In this case, create a new secret.

.. _cce_faq_00015__section165209286116:

Check Item 4: Whether the Node Disk Space Is Insufficient
---------------------------------------------------------

If information similar to the following is displayed in the Kubernetes events, it means that there is no disk space left for storing the image. As a result, the image will fail to be pulled. In this case, clear the image or expand the disk space to resolve this issue.

.. code-block::

   Failed create pod sandbox: rpc error: code = Unknown desc = failed to create a sandbox for pod "nginx-6dc48bf8b6-l8xrw": Error response from daemon: mkdir xxxxx: no space left on device

You can obtain the disk space for storing images on a node by running the following command:

.. code-block::

   lvs

|image1|

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

.. _cce_faq_00015__section1026851817369:

Check Item 5: Whether the Remote Image Repository Uses an Unknown or Insecure Certificate
-----------------------------------------------------------------------------------------

If a pod tries to pull an image from a third-party repository with an unknown or insecure certificate, the image pulling will fail on the node. The pod events contain "Failed to pull the image" with the cause "x509: certificate signed by unknown authority".

.. note::

   The security of EulerOS 2.9 images has been improved by removing insecure or expired certificates from the system. While some third-party images on certain nodes may not report any errors, it is common for this type of error to occur in EulerOS 2.9. To fix the issue, you can perform the following operations.

**Solution**

#. Check the IP address and port number of the third-party image server for which the error message "unknown authority" is displayed.

   You can see the IP address and port number of the third-party image server for which the error is reported in the event "Failed to pull image".

   .. code-block::

      Failed to pull image "bitnami/redis-cluster:latest": rpc error: code = Unknown desc = error pulling image configuration: Get https://production.cloudflare.docker.com/registry-v2/docker/registry/v2/blobs/sha256/e8/e83853f03a2e792614e7c1e6de75d63e2d6d633b4e7c39b9d700792ee50f7b56/data?verify=1636972064-AQbl5RActnudDZV%2F3EShZwnqOe8%3D: x509: certificate signed by unknown authority

   The IP address of the third-party image server is *production.cloudflare.docker.com*, and the default HTTPS port number is **443**.

#. Load the root certificate of the third-party image server to the node where the third-party image is to be pulled.

   Run the following command on an EulerOS or CentOS node with *{server_url}:{server_port}* replaced with the IP address and port number obtained in the previous step, for example, **production.cloudflare.docker.com:443**.

   If the container engine of the node is containerd, replace **systemctl restart docker** with **systemctl restart containerd**.

   .. code-block::

      openssl s_client -showcerts -connect {server_url}:{server_port} < /dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > /etc/pki/ca-trust/source/anchors/tmp_ca.crt
      update-ca-trust
      systemctl restart docker

   Run the following command on an Ubuntu node:

   .. code-block::

      openssl s_client -showcerts -connect {server_url}:{server_port} < /dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > /usr/local/share/ca-certificates/tmp_ca.crt
      update-ca-trust
      systemctl restart docker

.. _cce_faq_00015__section1962218412226:

Check Item 6: Whether the Image Size Is Too Large
-------------------------------------------------

The pod events contain "Failed to pull image". This may be caused by a large image size.

.. code-block::

   Failed to pull image "XXX": rpc error: code = Unknown desc = context canceled

However, the image can be manually pulled by running the **docker pull** command on the node.

**Possible cause**

In Kubernetes clusters, there is a default timeout period for pulling images. If the image pulling progress is not updated within a certain period of time, the pulling will be canceled. If the node performance is poor or the image size is too large, the image may fail to be pulled and the workload may fail to be started.

**Solution**

-  **(Recommended)** Solution 1:

   #. Log in to the node and manually pull the image.

      -  Nodes that use containerd:

         .. code-block::

            crictl pull <image-address>

      -  Nodes that use Docker:

         .. code-block::

            docker pull <image-address>

   #. When creating a workload, ensure that **imagePullPolicy** is set to **IfNotPresent** (the default configuration). In this case, the workload can use the image that has been pulled to the local host.

-  (For clusters of v1.25 or later) Solution 2: Modify the configuration parameters of the node pool. The configuration parameters for nodes in the **DefaultPool** node pool cannot be modified.

   #. Log in to the CCE console.
   #. Click the cluster name to access the cluster console. In the navigation pane, choose **Nodes**. In the right pane, click the **Node Pools** tab.
   #. Locate the row containing the target node pool and click **Manage**.
   #. In the window that slides out from the right, modify the **image-pull-progress-timeout** parameter under **Docker/containerd**. This **image-pull-progress-timeout** parameter specifies the timeout interval for pulling an image.
   #. Click **OK**.

.. _cce_faq_00015__section19758182810816:

Check Item 7: Whether the Image Repository Can Be Accessed
----------------------------------------------------------

**Symptom**

An error message similar to the following is displayed during workload creation:

.. code-block::

   Failed to pull image "docker.io/bitnami/nginx:1.22.0-debian-11-r3": rpc error: code = Unknown desc = Error response from daemon: Get https://registry-1.docker.io/v2/: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)

**Possible cause**

The image repository cannot be accessed due to the network problems. SWR allows you to pull images only from the official Docker repository. To pull images from other repositories, you need to access the Internet.

**Solution**

-  Bind a public IP address to the node to which the image needs to be pulled.
-  Push images to SWR and then pull them from SWR to your nodes.

.. _cce_faq_00015__section13904181735115:

Check Item 8: Whether the Number of Public Image Pulls Reaches the Upper Limit
------------------------------------------------------------------------------

**Symptom**

An error message similar to the following is displayed during workload creation:

.. code-block::

   ERROR: toomanyrequests: Too Many Requests.

Or

.. code-block::

   you have reached your pull rate limit, you may increase the limit by authenticating an upgrading: https://www.docker.com/increase-rate-limits.

**Possible cause**

Docker Hub sets limits for the maximum number of container image pull requests. For details, see `Docker Hub pull usage and limits <https://docs.docker.com/docker-hub/usage/pulls/>`__.

**Solution**

Push the frequently used images to SWR and then pull them from SWR to your nodes.

.. |image1| image:: /_static/images/en-us_image_0000002218659182.jpg
