:original_name: cce_10_0047.html

.. _cce_10_0047:

Creating a Deployment
=====================

Scenario
--------

Deployments are workloads (for example, Nginx) that do not store any data or status. You can create Deployments on the CCE console or by running kubectl commands.

Prerequisites
-------------

-  Before creating a workload, you must have an available cluster. For details on how to create a cluster, see :ref:`Creating a CCE Standard/Turbo Cluster <cce_10_0028>`.
-  To enable public access to a workload, ensure that an EIP or load balancer has been bound to at least one node in the cluster.

   .. note::

      If a pod has multiple containers, ensure that the ports used by the containers do not conflict with each other. Otherwise, creating the Deployment will fail.

Using the CCE Console
---------------------

#. Log in to the CCE console.

#. Click the cluster name to go to the cluster console, choose **Workloads** in the navigation pane, and click the **Create Workload** in the upper right corner.

#. Set basic information about the workload.

   **Basic Info**

   -  **Workload Type**: Select **Deployment**. For details about workload types, see :ref:`Overview <cce_10_0006>`.
   -  **Workload Name**: Enter the name of the workload. Enter 1 to 63 characters starting with a lowercase letter and ending with a lowercase letter or digit. Only lowercase letters, digits, and hyphens (-) are allowed.
   -  **Namespace**: Select the namespace of the workload. The default value is **default**. You can also click **Create Namespace** to create one. For details, see :ref:`Creating a Namespace <cce_10_0278>`.
   -  **Pods**: Enter the number of pods of the workload.
   -  **Container Runtime**: A CCE standard cluster uses runC by default, whereas a CCE Turbo cluster supports both runC and Kata. For details about the differences, see :ref:`Secure Runtime and Common Runtime <cce_10_0463>`.
   -  **Time Zone Synchronization**: Specify whether to enable time zone synchronization. After time zone synchronization is enabled, the container and node use the same time zone. The time zone synchronization function depends on the local disk mounted to the container. Do not modify or delete the time zone. For details, see :ref:`Configuring Time Zone Synchronization <cce_10_0354>`.

   **Container Settings**

   -  Container Information

      Multiple containers can be configured in a pod. You can click **Add Container** on the right to configure multiple containers for the pod.

      -  **Basic Info**: Configure basic information about the container.

         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                         |
         +===================================+=====================================================================================================================================================================================================================================================================================================================================================================================================================================+
         | Container Name                    | Name the container.                                                                                                                                                                                                                                                                                                                                                                                                                 |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Pull Policy                       | Image update or pull policy. If you select **Always**, the image is pulled from the image repository each time. If you do not select **Always**, the existing image of the node is preferentially used. If the image does not exist, the image is pulled from the image repository.                                                                                                                                                 |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Image Name                        | Click **Select Image** and select the image used by the container.                                                                                                                                                                                                                                                                                                                                                                  |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | To use a third-party image, see :ref:`Using Third-Party Images <cce_10_0009>`.                                                                                                                                                                                                                                                                                                                                                      |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Image Tag                         | Select the image tag to be deployed.                                                                                                                                                                                                                                                                                                                                                                                                |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | CPU Quota                         | -  **Request**: minimum number of CPU cores required by a container. The default value is 0.25 cores.                                                                                                                                                                                                                                                                                                                               |
         |                                   | -  **Limit**: maximum number of CPU cores that can be used by a container. This prevents containers from using excessive resources.                                                                                                                                                                                                                                                                                                 |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | If **Request** and **Limit** are not specified, the quota is not limited. For more information and suggestions about **Request** and **Limit**, see :ref:`Configuring Container Specifications <cce_10_0163>`.                                                                                                                                                                                                                      |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Memory Quota                      | -  **Request**: minimum amount of memory required by a container. The default value is 512 MiB.                                                                                                                                                                                                                                                                                                                                     |
         |                                   | -  **Limit**: maximum amount of memory available for a container. When memory usage exceeds the specified memory limit, the container will be terminated.                                                                                                                                                                                                                                                                           |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | If **Request** and **Limit** are not specified, the quota is not limited. For more information and suggestions about **Request** and **Limit**, see :ref:`Configuring Container Specifications <cce_10_0163>`.                                                                                                                                                                                                                      |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | (Optional) GPU Quota              | Configurable only when the cluster contains GPU nodes and the :ref:`CCE AI Suite (NVIDIA GPU) <cce_10_0141>` add-on is installed.                                                                                                                                                                                                                                                                                                   |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | -  **All**: No GPU will be used.                                                                                                                                                                                                                                                                                                                                                                                                    |
         |                                   | -  **Dedicated**: GPU resources are dedicated for the container.                                                                                                                                                                                                                                                                                                                                                                    |
         |                                   | -  **Shared**: percentage of GPU resources used by the container. For example, if this parameter is set to **10%**, the container uses 10% of GPU resources.                                                                                                                                                                                                                                                                        |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | For details about how to use GPUs in the cluster, see :ref:`Default GPU Scheduling in Kubernetes <cce_10_0345>`.                                                                                                                                                                                                                                                                                                                    |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | (Optional) Privileged Container   | Programs in a privileged container have certain privileges.                                                                                                                                                                                                                                                                                                                                                                         |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | If **Privileged Container** is enabled, the container is assigned privileges. For example, privileged containers can manipulate network devices on the host machine and modify kernel parameters.                                                                                                                                                                                                                                   |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | (Optional) Init Container         | Whether to use the container as an init container. An init container does not support health check.                                                                                                                                                                                                                                                                                                                                 |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | An init container is a special container that runs before other app containers in a pod are started. Each pod can contain multiple containers. In addition, a pod can contain one or more init containers. Application containers in a pod are started and run only after the running of all init containers completes. For details, see `Init Containers <https://kubernetes.io/docs/concepts/workloads/pods/init-containers/>`__. |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

      -  (Optional) **Lifecycle**: Configure operations to be performed in a specific phase of the container lifecycle, such as Startup Command, Post-Start, and Pre-Stop. For details, see :ref:`Configuring Container Lifecycle Parameters <cce_10_0105>`.

      -  (Optional) **Health Check**: Set the liveness probe, ready probe, and startup probe as required. For details, see :ref:`Configuring Container Health Check <cce_10_0112>`.

      -  (Optional) **Environment Variables**: Configure variables for the container running environment using key-value pairs. These variables transfer external information to containers running in pods and can be flexibly modified after application deployment. For details, see :ref:`Configuring Environment Variables <cce_10_0113>`.

      -  (Optional) **Data Storage**: Mount local storage or cloud storage to the container. The application scenarios and mounting modes vary with the storage type. For details, see :ref:`Storage <cce_10_0374>`.

         .. note::

            If the workload contains more than one pod, EVS volumes cannot be mounted.

      -  (Optional) **Security Context**: Assign container permissions to protect the system and other containers from being affected. Enter the user ID to assign container permissions and prevent systems and other containers from being affected.

      -  (Optional) **Logging**: Report standard container output logs to AOM by default, without requiring manual settings. You can manually configure the log collection path. For details, see :ref:`Collecting Container Logs Using ICAgent <cce_10_0018>`.

         To disable the standard output of the current workload, add the annotation **kubernetes.AOM.log.stdout: []** in :ref:`Labels and Annotations <cce_10_0047__li179714209414>`. For details about how to use this annotation, see :ref:`Table 1 <cce_10_0386__table194691458405>`.

   -  **Image Access Credential**: Select the credential used for accessing the image repository. The default value is **default-secret**. You can use default-secret to access images in SWR. For details about **default-secret**, see :ref:`default-secret <cce_10_0388__section11760122012591>`.

   -  (Optional) **GPU**: **All** is selected by default. The workload instance will be scheduled to the node of the specified GPU type.

   **(Optional) Service Settings**

   A Service provides external access for pods. With a static IP address, a Service forwards access traffic to pods and automatically balances load for these pods.

   You can also create a Service after creating a workload. For details about Services of different types, see :ref:`Overview <cce_10_0249>`.

   **(Optional) Advanced Settings**

   -  **Upgrade**: Specify the upgrade mode and parameters of the workload. **Rolling upgrade** and **Replace upgrade** are available. For details, see :ref:`Configuring Workload Upgrade Policies <cce_10_0397>`.

   -  **Scheduling**: Configure affinity and anti-affinity policies for flexible workload scheduling. Load affinity and node affinity are provided.

      -  **Load Affinity**: Common load affinity policies are offered for quick load affinity deployment.

         -  **Multi-AZ deployment is preferred**: Workload pods are preferentially scheduled to nodes in different AZs through pod anti-affinity (**podAntiAffinity**). If all the nodes in the cluster are deployed in the same AZ, the pods will be scheduled to that AZ but onto different nodes for high availability. If there are fewer nodes than pods, the extra pods will fail to run.
         -  **Forcible multi-AZ deployment**: Workload pods are forcibly scheduled to nodes in different AZs through pod anti-affinity (**podAntiAffinity**). If there are fewer AZs than pods, the extra pods will fail to run.
         -  **Custom policies**: Affinity and anti-affinity policies can be customized as needed. For details, see :ref:`Scheduling Policies (Affinity/Anti-affinity) <cce_10_0232>`.

      -  **Node Affinity**: Common load affinity policies are offered for quick load affinity deployment.

         -  **Node Affinity**: Workload pods can be deployed on specified nodes through node affinity (**nodeAffinity**). If no node is specified, the pods will be randomly scheduled based on the default scheduling policy of the cluster.
         -  **Specified node pool scheduling**: Workload pods can be deployed in a specified node pool through node affinity (**nodeAffinity**). If no node pool is specified, the pods will be randomly scheduled based on the default scheduling policy of the cluster.
         -  **Custom policies**: Affinity and anti-affinity policies can be customized as needed. For details, see :ref:`Scheduling Policies (Affinity/Anti-affinity) <cce_10_0232>`.

   -  **Toleration**: Using both taints and tolerations allows (not forcibly) the pod to be scheduled to a node with the matching taints, and controls the pod eviction policies after the node where the pod is located is tainted. For details, see :ref:`Configuring Tolerance Policies <cce_10_0728>`.

   -  .. _cce_10_0047__li179714209414:

      **Labels and Annotations**: Add labels or annotations for pods using key-value pairs. After entering the key and value, click **Confirm**. For details about how to use and configure labels and annotations, see :ref:`Configuring Labels and Annotations <cce_10_0386>`.

   -  **DNS**: Configure a separate DNS policy for the workload. For details, see :ref:`DNS Configuration <cce_10_0365>`.

   -  **Network Configuration**

      -  Pod ingress/egress bandwidth limitation: You can set ingress/egress bandwidth limitation for pods. For details, see :ref:`Configuring QoS for a Pod <cce_10_0382>`.
      -  IPv6 shared bandwidth: available only for clusters that support this function. After this function is enabled, you can configure a shared bandwidth for a pod with IPv6 dual-stack ENIs. For details, see :ref:`Configuring Shared Bandwidth for a Pod with IPv6 Dual-Stack ENIs <cce_10_0604>`.

#. Click **Create Workload** in the lower right corner.

.. _cce_10_0047__section155246177178:

Using kubectl
-------------

The following procedure uses Nginx as an example to describe how to create a workload using kubectl.

#. Use kubectl to access the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create and edit the **nginx-deployment.yaml** file. **nginx-deployment.yaml** is an example file name, and you can rename it as required.

   **vi nginx-deployment.yaml**

   The following is an example YAML file. For more information about Deployments, see `Kubernetes documentation <https://kubernetes.io/docs/concepts/workloads/controllers/deployment/>`__.

   .. code-block::

      apiVersion: apps/v1
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
            - image: nginx    # If you use an image in My Images, obtain the image path from SWR.
              imagePullPolicy: Always
              name: nginx
            imagePullSecrets:
            - name: default-secret

   For details about the parameters, see :ref:`Table 1 <cce_10_0047__table132326831016>`.

   .. _cce_10_0047__table132326831016:

   .. table:: **Table 1** Deployment YAML parameters

      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Parameter             | Description                                                                                                                                                                                                                                                                                                                            | Mandatory/Optional    |
      +=======================+========================================================================================================================================================================================================================================================================================================================================+=======================+
      | apiVersion            | API version.                                                                                                                                                                                                                                                                                                                           | Mandatory             |
      |                       |                                                                                                                                                                                                                                                                                                                                        |                       |
      |                       | .. note::                                                                                                                                                                                                                                                                                                                              |                       |
      |                       |                                                                                                                                                                                                                                                                                                                                        |                       |
      |                       |    Set this parameter based on the cluster version.                                                                                                                                                                                                                                                                                    |                       |
      |                       |                                                                                                                                                                                                                                                                                                                                        |                       |
      |                       |    -  For clusters of v1.17 or later, the apiVersion format of Deployments is **apps/v1**.                                                                                                                                                                                                                                             |                       |
      |                       |    -  For clusters of v1.15 or earlier, the apiVersion format of Deployments is **extensions/v1beta1**.                                                                                                                                                                                                                                |                       |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | kind                  | Type of a created object.                                                                                                                                                                                                                                                                                                              | Mandatory             |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | metadata              | Metadata of a resource object.                                                                                                                                                                                                                                                                                                         | Mandatory             |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | name                  | Name of the Deployment.                                                                                                                                                                                                                                                                                                                | Mandatory             |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | spec                  | Detailed description of the Deployment.                                                                                                                                                                                                                                                                                                | Mandatory             |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | replicas              | Number of pods.                                                                                                                                                                                                                                                                                                                        | Mandatory             |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | selector              | Determines container pods that can be managed by the Deployment.                                                                                                                                                                                                                                                                       | Mandatory             |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | strategy              | Upgrade mode. Possible values:                                                                                                                                                                                                                                                                                                         | Optional              |
      |                       |                                                                                                                                                                                                                                                                                                                                        |                       |
      |                       | -  RollingUpdate                                                                                                                                                                                                                                                                                                                       |                       |
      |                       | -  ReplaceUpdate                                                                                                                                                                                                                                                                                                                       |                       |
      |                       |                                                                                                                                                                                                                                                                                                                                        |                       |
      |                       | By default, rolling update is used.                                                                                                                                                                                                                                                                                                    |                       |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | template              | Detailed description of a created container pod.                                                                                                                                                                                                                                                                                       | Mandatory             |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | metadata              | Metadata.                                                                                                                                                                                                                                                                                                                              | Mandatory             |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | labels                | **metadata.labels**: Container labels.                                                                                                                                                                                                                                                                                                 | Optional              |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | spec:                 | -  **image** (mandatory): Name of a container image.                                                                                                                                                                                                                                                                                   | Mandatory             |
      |                       | -  **imagePullPolicy** (optional): Policy for obtaining an image. The options include **Always** (attempting to download images each time), **Never** (only using local images), and **IfNotPresent** (using local images if they are available; downloading images if local images are unavailable). The default value is **Always**. |                       |
      | containers            | -  **name** (mandatory): Container name.                                                                                                                                                                                                                                                                                               |                       |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | imagePullSecrets      | Name of the secret used during image pulling. If a private image is used, this parameter is mandatory.                                                                                                                                                                                                                                 | Optional              |
      |                       |                                                                                                                                                                                                                                                                                                                                        |                       |
      |                       | -  To pull an image from the Software Repository for Container (SWR), set this parameter to **default-secret**.                                                                                                                                                                                                                        |                       |
      |                       | -  To pull an image from a third-party image repository, set this parameter to the name of the created secret.                                                                                                                                                                                                                         |                       |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+

#. Create a Deployment.

   **kubectl create -f nginx-deployment.yaml**

   If the following information is displayed, the Deployment is being created.

   .. code-block::

      deployment "nginx" created

#. Obtain the Deployment status.

   **kubectl get deployment**

   If the following information is displayed, the Deployment is running.

   .. code-block::

      NAME           READY     UP-TO-DATE   AVAILABLE   AGE
      nginx          1/1       1            1           4m5s

   **Parameter description**

   -  **NAME**: Name of the application running in the pod.
   -  **READY**: indicates the number of available workloads. The value is displayed as "the number of available pods/the number of expected pods".
   -  **UP-TO-DATE**: indicates the number of replicas that have been updated.
   -  **AVAILABLE**: indicates the number of available pods.
   -  **AGE**: period the Deployment keeps running

#. If the Deployment will be accessed through a ClusterIP or NodePort Service, configure the access mode. For details, see :ref:`Network <cce_10_0020>`.
