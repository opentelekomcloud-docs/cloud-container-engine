:original_name: cce_faq_00015.html

.. _cce_faq_00015:

What Should I Do If a Pod Fails to Pull the Image?
==================================================

Fault Locating
--------------

When a workload enters the state of "Pod not ready: Back-off pulling image "*xxxxx*", a Kubernetes event of **PodsFailed to pull image** or **Failed to re-pull image** will be reported. For details about how to view Kubernetes events, see :ref:`Viewing Pod Events <cce_faq_00134__section13566155892120>`.

Troubleshooting Process
-----------------------

Determine the cause based on the event information, as listed in :ref:`Table 1 <cce_faq_00015__table230510269532>`.

.. _cce_faq_00015__table230510269532:

.. table:: **Table 1** FailedPullImage

   +---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | Event Information                                                                                                                                                                                                                                                               | Cause and Solution                                                                                                                       |
   +=================================================================================================================================================================================================================================================================================+==========================================================================================================================================+
   | Failed to pull image "*xxx*": rpc error: code = Unknown desc = Error response from daemon: Get *xxx*: denied: You may not login yet                                                                                                                                             | You have not logged in to the image repository.                                                                                          |
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
   | Failed to pull image "docker.io/bitnami/nginx:1.22.0-debian-11-r3": rpc error: code = Unknown desc = Error response from daemon: Get https://registry-1.docker.io/v2/: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers) | :ref:`Check Item 7: Connection to the Image Repository <cce_faq_00015__section19758182810816>`                                           |
   +---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | ERROR: toomanyrequests: Too Many Requests.                                                                                                                                                                                                                                      | The rate is limited because the number of image pull times reaches the upper limit.                                                      |
   |                                                                                                                                                                                                                                                                                 |                                                                                                                                          |
   | Or                                                                                                                                                                                                                                                                              | :ref:`Check Item 8: Whether the Number of Public Image Pull Times Reaches the Upper Limit <cce_faq_00015__section13904181735115>`        |
   |                                                                                                                                                                                                                                                                                 |                                                                                                                                          |
   | you have reached your pull rate limit, you may increase the limit by authenticating an upgrading                                                                                                                                                                                |                                                                                                                                          |
   +---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_faq_00015__section629791052512:

Check Item 1: Whether **imagePullSecret** Is Specified When You Use kubectl to Create a Workload
------------------------------------------------------------------------------------------------

If the workload status is abnormal and a Kubernetes event is displayed indicating that the pod fails to pull the image, check whether the **imagePullSecrets** field exists in the YAML file.

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

-  If an image needs to be pulled from a third-party image repository, the **imagePullSecrets** parameter must be set to the created secret name.

   When you use kubectl to create a workload from a third-party image, specify the **imagePullSecret** field, in which **name** indicates the name of the secret used to pull the image.

.. _cce_faq_00015__section819316261313:

Check Item 2: Whether the Image Address Is Correct When a Third-Party Image Is Used
-----------------------------------------------------------------------------------

CCE allows you to create workloads using images pulled from third-party image repositories.

Enter the third-party image address according to requirements. The format must be **ip:port/path/name:version** or **name:version**. If no tag is specified, **latest** is used by default.

-  For a private repository, enter an image address in the format of **ip:port/path/name:version**.
-  For an open-source Docker repository, enter an image address in the format of **name:version**, for example, **nginx:latest**.

The following information is displayed when you fail to pull an image due to incorrect image address provided.

.. code-block::

   Failed to pull image "nginx:v1.1": rpc error: code = Unknown desc = Error response from daemon: Get https://registry-1.docker.io/v2/: dial tcp: lookup registry-1.docker.io: no such host

**Solution**

You can either edit your YAML file to change the image address or log in to the CCE console to replace the image on the **Upgrade** tab on the workload details page.

.. _cce_faq_00015__section9312113135616:

Check Item 3: Whether an Incorrect Secret Is Used When a Third-Party Image Is Used
----------------------------------------------------------------------------------

Generally, a third-party image repository can be accessed only after authentication (using your account and password). CCE uses the secret authentication mode to pull images. Therefore, you need to create a secret for an image repository before pulling images from the repository.

**Solution**

If your secret is incorrect, images will fail to be pulled. In this case, create a new secret.

.. _cce_faq_00015__section165209286116:

Check Item 4: Whether the Node Disk Space Is Insufficient
---------------------------------------------------------

If the Kubernetes event contains information "no space left on device", there is no disk space left for storing the image. As a result, the image will fail to be pulled. In this case, clear the image or expand the disk space to resolve this issue.

.. code-block::

   Failed create pod sandbox: rpc error: code = Unknown desc = failed to create a sandbox for pod "nginx-6dc48bf8b6-l8xrw": Error response from daemon: mkdir xxxxx: no space left on device

Run the following command to obtain the disk space for storing images on a node:

.. code-block::

   lvs

|image1|

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

.. _cce_faq_00015__section1026851817369:

Check Item 5: Whether the Remote Image Repository Uses an Unknown or Insecure Certificate
-----------------------------------------------------------------------------------------

When a pod pulls an image from a third-party image repository that uses an unknown or insecure certificate, the image fails to be pulled from the node. The pod event list contains the event "Failed to pull the image" with the cause "x509: certificate signed by unknown authority".

.. note::

   The security of EulerOS 2.9 images is enhanced. Some insecure or expired certificates are removed from the system. It is normal that this error is reported in EulerOS 2.9 but not or some third-party images on other types of nodes. You can also perform the following operations to rectify the fault.

**Solution**

#. Check the IP address and port number of the third-party image server for which the error message "unknown authority" is displayed.

   You can see the IP address and port number of the third-party image server for which the error is reported in the event information "Failed to pull image".

   .. code-block::

      Failed to pull image "bitnami/redis-cluster:latest": rpc error: code = Unknown desc = error pulling image configuration: Get https://production.cloudflare.docker.com/registry-v2/docker/registry/v2/blobs/sha256/e8/e83853f03a2e792614e7c1e6de75d63e2d6d633b4e7c39b9d700792ee50f7b56/data?verify=1636972064-AQbl5RActnudDZV%2F3EShZwnqOe8%3D: x509: certificate signed by unknown authority

   The IP address of the third-party image server is *production.cloudflare.docker.com*, and the default HTTPS port number is *443*.

#. Load the root certificate of the third-party image server to the node where the third-party image is to be downloaded.

   Run the following commands on the EulerOS and CentOS nodes with *{server_url}:{server_port}* replaced with the IP address and port number obtained in Step 1, for example, **production.cloudflare.docker.com:443**:

   If the container engine of the node is containerd, replace **systemctl restart docker** with **systemctl restart containerd**.

   .. code-block::

      openssl s_client -showcerts -connect {server_url}:{server_port} < /dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > /etc/pki/ca-trust/source/anchors/tmp_ca.crt
      update-ca-trust
      systemctl restart docker

   Run the following command on Ubuntu nodes:

   .. code-block::

      openssl s_client -showcerts -connect {server_url}:{server_port} < /dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > /usr/local/share/ca-certificates/tmp_ca.crt
      update-ca-trust
      systemctl restart docker

.. _cce_faq_00015__section1962218412226:

Check Item 6: Whether the Image Size Is Too Large
-------------------------------------------------

The pod event list contains the event "Failed to pull image". This may be caused by a large image size.

.. code-block::

   Failed to pull image "XXX": rpc error: code = Unknown desc = context canceled

However, the image can be manually pulled by running the **docker pull** command.

**Possible Causes**

In Kubernetes clusters, there is a default timeout period for pulling images. If the image pulling progress is not updated within a certain period of time, the download will be canceled. If the node performance is poor or the image size is too large, the image may fail to be pulled and the workload may fail to be started.

**Solution**

-  Solution 1 (recommended):

   #. Log in to the node and manually pull the image.

      -  containerd nodes:

         .. code-block::

            crictl pull <image-address>

      -  Docker nodes:

         .. code-block::

            docker pull <image-address>

   #. When creating a workload, ensure that **imagePullPolicy** is set to **IfNotPresent** (the default configuration). In this case, the workload uses the image that has been pulled to the local host.

-  Solution 2 (applies to clusters of v1.25 or later): Modify the configuration parameters of the node pools. The configuration parameters for nodes in the **DefaultPool** node pool cannot be modified.

   #. Log in to the CCE console.
   #. Click the cluster name to access the cluster console. Choose **Nodes** in the navigation pane and click the **Node Pools** tab.
   #. Locate the row that contains the target node pool and click **Manage**.
   #. In the window that slides out from the right, modify the **image-pull-progress-timeout** parameter under **Docker/containerd**. This parameter specifies the timeout interval for pulling an image.
   #. Click **OK**.

.. _cce_faq_00015__section19758182810816:

Check Item 7: Connection to the Image Repository
------------------------------------------------

**Symptom**

The following error message is displayed during workload creation:

.. code-block::

   Failed to pull image "docker.io/bitnami/nginx:1.22.0-debian-11-r3": rpc error: code = Unknown desc = Error response from daemon: Get https://registry-1.docker.io/v2/: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)

**Possible Causes**

Failed to connect to the image repository due to the disconnected network. SWR allows you to pull images only from the official Docker repository. For image pulls from other repositories, you need to access the Internet.

**Solution**

-  Bind a public IP address to the node which needs to pull the images.
-  Upload the image to SWR and then pull the image from SWR.

.. _cce_faq_00015__section13904181735115:

Check Item 8: Whether the Number of Public Image Pull Times Reaches the Upper Limit
-----------------------------------------------------------------------------------

**Symptom**

The following error message is displayed during workload creation:

.. code-block::

   ERROR: toomanyrequests: Too Many Requests.

Or

.. code-block::

   you have reached your pull rate limit, you may increase the limit by authenticating an upgrading: https://www.docker.com/increase-rate-limits.

**Possible Causes**

Docker Hub sets the maximum number of container image pull requests. For details, see `Understanding Your Docker Hub Rate Limit <https://www.docker.com/increase-rate-limits/>`__.

**Solution**

Push the frequently used image to SWR and then pull the image from SWR.

.. |image1| image:: /_static/images/en-us_image_0000001851585232.jpg
