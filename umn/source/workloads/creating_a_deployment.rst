:original_name: cce_10_0047.html

.. _cce_10_0047:

Creating a Deployment
=====================

Scenario
--------

Deployments are workloads (for example, Nginx) that do not store any data or status. You can create Deployments on the CCE console or by running kubectl commands.

Prerequisites
-------------

-  Before creating a containerized workload, you must have an available cluster. For details on how to create a cluster, see :ref:`Creating a CCE Cluster <cce_10_0028>`.
-  To enable public access to a workload, ensure that an EIP or load balancer has been bound to at least one node in the cluster.

   .. note::

      If a pod has multiple containers, ensure that the ports used by the containers do not conflict with each other. Otherwise, creating the Deployment will fail.

Using the CCE Console
---------------------

#. Log in to the CCE console.

#. Click the cluster name to access the cluster details page, choose **Workloads** in the navigation pane, and click the **Create Workload** in the upper right corner.

#. Set basic information about the workload.

   **Basic Info**

   -  **Workload Type**: Select **Deployment**. For details about workload types, see :ref:`Overview <cce_10_0006>`.
   -  **Workload Name**: Enter the name of the workload.
   -  **Namespace**: Select the namespace of the workload. The default value is **default**. You can also click **Create Namespace** to create one. For details, see :ref:`Creating a Namespace <cce_10_0278>`.
   -  **Pods**: Enter the number of pods.
   -  **Container Runtime**: A CCE cluster uses runC by default, whereas a CCE Turbo cluster supports both runC and Kata. For details about the differences between runC and Kata, see :ref:`Kata Containers and Common Containers <cce_10_0463>`.
   -  **Time Zone Synchronization**: Specify whether to enable time zone synchronization. After time zone synchronization is enabled, the container and node use the same time zone. The time zone synchronization function depends on the local disk mounted to the container. Do not modify or delete the time zone. For details, see :ref:`Configuring Time Zone Synchronization <cce_10_0354>`.

   **Container Settings**

   -  Container Information

      Multiple containers can be configured in a pod. You can click **Add Container** on the right to configure multiple containers for the pod.

      -  **Basic Info**: See :ref:`Setting Basic Container Information <cce_10_0396>`.
      -  **Lifecycle**: See :ref:`Setting Container Lifecycle Parameters <cce_10_0105>`.
      -  **Health Check**: See :ref:`Setting Health Check for a Container <cce_10_0112>`.
      -  **Environment Variables**: See :ref:`Setting an Environment Variable <cce_10_0113>`.
      -  **Data Storage**: See :ref:`Overview <cce_10_0307>`.

         .. note::

            If the workload contains more than one pod, EVS volumes cannot be mounted.

      -  **Security Context**: Set container permissions to protect the system and other containers from being affected. Enter the user ID to set container permissions and prevent systems and other containers from being affected.
      -  **Logging**: See :ref:`Using ICAgent to Collect Container Logs <cce_10_0018>`.

   -  **Image Access Credential**: Select the credential used for accessing the image repository. The default value is **default-secret**. You can use default-secret to access images in SWR. For details about **default-secret**, see :ref:`default-secret <cce_10_0388__section11760122012591>`.

   -  **GPU graphics card**: **All** is selected by default. The workload instance will be scheduled to the node with the specified GPU graphics card type.

   **Service Settings**

   A Service is used for pod access. With a fixed IP address, a Service forwards access traffic to pods and performs load balancing for these pods.

   You can also create a Service after creating a workload. For details about the Service, see :ref:`Service Overview <cce_10_0249>`.

   **Advanced Settings**

   -  **Upgrade**: See :ref:`Configuring the Workload Upgrade Policy <cce_10_0397>`.
   -  **Scheduling**: See :ref:`Scheduling Policy (Affinity/Anti-affinity) <cce_10_0232>`.
   -  **Labels and Annotations**: See :ref:`Pod Labels and Annotations <cce_10_0386>`.
   -  **Toleration**: Using both taints and tolerations allows (not forcibly) the pod to be scheduled to a node with the matching taints, and controls the pod eviction policies after the node where the pod is located is tainted. For details, see :ref:`Tolerations <cce_10_0352__section2047442210417>`.
   -  **DNS**: See :ref:`DNS Configuration <cce_10_0365>`.

#. Click **Create Workload** in the lower right corner.

.. _cce_10_0047__section155246177178:

Using kubectl
-------------

The following procedure uses Nginx as an example to describe how to create a workload using kubectl.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create and edit the **nginx-deployment.yaml** file. **nginx-deployment.yaml** is an example file name. You can rename it as required.

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

   For details about these parameters, see :ref:`Table 1 <cce_10_0047__table132326831016>`.

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
      | Spec                  | Detailed description of the Deployment.                                                                                                                                                                                                                                                                                                | Mandatory             |
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

#. Query the Deployment status.

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

#. If the Deployment will be accessed through a ClusterIP or NodePort Service, add the corresponding Service. For details, see :ref:`Networking <cce_10_0020>`.
