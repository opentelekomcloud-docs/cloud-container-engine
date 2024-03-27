:original_name: cce_10_0216.html

.. _cce_10_0216:

Creating a DaemonSet
====================

Scenario
--------

CCE provides deployment and management capabilities for multiple types of containers and supports features of container workloads, including creation, configuration, monitoring, scaling, upgrade, uninstall, service discovery, and load balancing.

DaemonSet ensures that only one pod runs on all or some nodes. When a node is added to a cluster, a new pod is also added for the node. When a node is removed from a cluster, the pod is also reclaimed. If a DaemonSet is deleted, all pods created by it will be deleted.

The typical application scenarios of a DaemonSet are as follows:

-  Run the cluster storage daemon, such as glusterd or Ceph, on each node.
-  Run the log collection daemon, such as Fluentd or Logstash, on each node.
-  Run the monitoring daemon, such as Prometheus Node Exporter, collectd, Datadog agent, New Relic agent, or Ganglia (gmond), on each node.

You can deploy a DaemonSet for each type of daemons on all nodes, or deploy multiple DaemonSets for the same type of daemons. In the second case, DaemonSets have different flags and different requirements on memory and CPU for different hardware types.

Prerequisites
-------------

You must have one cluster available before creating a DaemonSet. For details on how to create a cluster, see :ref:`Creating a Cluster <cce_10_0028>`.

Using the CCE Console
---------------------

#. Log in to the CCE console.

#. Click the cluster name to go to the cluster console, choose **Workloads** in the navigation pane, and click **Create Workload** in the upper right corner.

#. Set basic information about the workload.

   **Basic Info**

   -  **Workload Type**: Select **DaemonSet**. For details about workload types, see :ref:`Overview <cce_10_0006>`.
   -  **Workload Name**: Enter the name of the workload. Enter 1 to 63 characters starting with a lowercase letter and ending with a lowercase letter or digit. Only lowercase letters, digits, and hyphens (-) are allowed.
   -  **Namespace**: Select the namespace of the workload. The default value is **default**. You can also click **Create Namespace** to create one. For details, see :ref:`Creating a Namespace <cce_10_0278>`.
   -  **Container Runtime**: A CCE cluster uses runC by default, whereas a CCE Turbo cluster supports both runC and Kata. For details about the differences, see :ref:`Kata Runtime and Common Runtime <cce_10_0463>`.
   -  **Time Zone Synchronization**: Specify whether to enable time zone synchronization. After time zone synchronization is enabled, the container and node use the same time zone. The time zone synchronization function depends on the local disk mounted to the container. Do not modify or delete the time zone. For details, see :ref:`Configuring Time Zone Synchronization <cce_10_0354>`.

   **Container Settings**

   -  **Container Information**

      Multiple containers can be configured in a pod. You can click **Add Container** on the right to configure multiple containers for the pod.

      -  **Basic Info**: Configure basic information about the container.

         +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                        |
         +===================================+====================================================================================================================================================================================================================================================================================================================================================================================================================================+
         | Container Name                    | Name the container.                                                                                                                                                                                                                                                                                                                                                                                                                |
         +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Pull Policy                       | Image update or pull policy. If you select **Always**, the image is pulled from the image repository each time. If you do not select **Always**, the existing image of the node is preferentially used. If the image does not exist, the image is pulled from the image repository.                                                                                                                                                |
         +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Image Name                        | Click **Select Image** and select the image used by the container.                                                                                                                                                                                                                                                                                                                                                                 |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                    |
         |                                   | To use a third-party image, see :ref:`Using Third-Party Images <cce_10_0009>`.                                                                                                                                                                                                                                                                                                                                                     |
         +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Image Tag                         | Select the image tag to be deployed.                                                                                                                                                                                                                                                                                                                                                                                               |
         +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | CPU Quota                         | -  **Request**: minimum number of CPU cores required by a container. The default value is 0.25 cores.                                                                                                                                                                                                                                                                                                                              |
         |                                   | -  **Limit**: maximum number of CPU cores available for a container. Do not leave **Limit** unspecified. Otherwise, intensive use of container resources will occur and your workload may exhibit unexpected behavior.                                                                                                                                                                                                             |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                    |
         |                                   | If **Request** and **Limit** are not specified, the quota is not limited. For more information and suggestions about **Request** and **Limit**, see :ref:`Setting Container Specifications <cce_10_0163>`.                                                                                                                                                                                                                         |
         +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Memory Quota                      | -  **Request**: minimum amount of memory required by a container. The default value is 512 MiB.                                                                                                                                                                                                                                                                                                                                    |
         |                                   | -  **Limit**: maximum amount of memory available for a container. When memory usage exceeds the specified memory limit, the container will be terminated.                                                                                                                                                                                                                                                                          |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                    |
         |                                   | If **Request** and **Limit** are not specified, the quota is not limited. For more information and suggestions about **Request** and **Limit**, see :ref:`Setting Container Specifications <cce_10_0163>`.                                                                                                                                                                                                                         |
         +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | (Optional) GPU Quota              | Configurable only when the cluster contains GPU nodes and the :ref:`CCE AI Suite (NVIDIA GPU) <cce_10_0141>` add-on is installed.                                                                                                                                                                                                                                                                                                  |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                    |
         |                                   | -  **All**: The GPU is not used.                                                                                                                                                                                                                                                                                                                                                                                                   |
         |                                   | -  **Dedicated**: GPU resources are exclusively used by the container.                                                                                                                                                                                                                                                                                                                                                             |
         |                                   | -  **Shared**: percentage of GPU resources used by the container. For example, if this parameter is set to **10%**, the container uses 10% of GPU resources.                                                                                                                                                                                                                                                                       |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                    |
         |                                   | For details about how to use GPU in the cluster, see :ref:`Default GPU Scheduling in Kubernetes <cce_10_0345>`.                                                                                                                                                                                                                                                                                                                    |
         +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | (Optional) Privileged Container   | Programs in a privileged container have certain privileges.                                                                                                                                                                                                                                                                                                                                                                        |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                    |
         |                                   | If **Privileged Container** is enabled, the container is assigned privileges. For example, privileged containers can manipulate network devices on the host machine and modify kernel parameters.                                                                                                                                                                                                                                  |
         +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | (Optional) Init Container         | Indicates whether to use the container as an init container. The init container does not support health check.                                                                                                                                                                                                                                                                                                                     |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                    |
         |                                   | An init container is a special container that runs before other app containers in a pod are started. Each pod can contain multiple containers. In addition, a pod can contain one or more Init containers. Application containers in a pod are started and run only after the running of all Init containers completes. For details, see `Init Container <https://kubernetes.io/docs/concepts/workloads/pods/init-containers/>`__. |
         +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

      -  (Optional) **Lifecycle**: Configure operations to be performed in a specific phase of the container lifecycle, such as Startup Command, Post-Start, and Pre-Stop. For details, see :ref:`Setting Container Lifecycle Parameters <cce_10_0105>`.

      -  (Optional) **Health Check**: Set the liveness probe, ready probe, and startup probe as required. For details, see :ref:`Setting Health Check for a Container <cce_10_0112>`.

      -  (Optional) **Environment Variables**: Set variables for the container running environment using key-value pairs. These variables transfer external information to containers running in pods and can be flexibly modified after application deployment. For details, see :ref:`Setting an Environment Variable <cce_10_0113>`.

      -  (Optional) **Data Storage**: Mount local storage or cloud storage to the container. The application scenarios and mounting modes vary with the storage type. For details, see :ref:`Storage <cce_10_0374>`.

      -  (Optional) **Security Context**: Set container permissions to protect the system and other containers from being affected. Enter the user ID to set container permissions and prevent systems and other containers from being affected.

      -  (Optional) **Logging**: Report container stdout streams to AOM by default and require no manual settings. You can manually configure the log collection path. For details, see :ref:`Using ICAgent to Collect Container Logs <cce_10_0018>`.

         To disable the standard output of the current workload, add the annotation kubernetes.AOM.log.stdout: [] in :ref:`Labels and Annotations <cce_10_0047__li179714209414>`. For details about how to use this annotation, see :ref:`Table 1 <cce_10_0386__table194691458405>`.

   -  **Image Access Credential**: Select the credential used for accessing the image repository. The default value is **default-secret**. You can use default-secret to access images in SWR. For details about **default-secret**, see :ref:`default-secret <cce_10_0388__section11760122012591>`.

   -  (Optional) **GPU**: **All** is selected by default. The workload instance will be scheduled to the node with the specified GPU graphics card type.

   **(Optional) Service Settings**

   A Service provides external access for pods. With a static IP address, a Service forwards access traffic to pods and performs automatic load balancing for these pods.

   You can also create a Service after creating a workload. For details about Services of different types, see :ref:`Overview <cce_10_0249>`.

   **(Optional) Advanced Settings**

   -  **Upgrade**: Specify the upgrade mode and upgrade parameters of the workload. **Rolling upgrade** and **Replace upgrade** are supported. For details, see :ref:`Configuring the Workload Upgrade Policy <cce_10_0397>`.

   -  **Scheduling**: Configure affinity and anti-affinity policies for flexible workload scheduling. Node affinity, pod affinity, and pod anti-affinity are supported. For details, see :ref:`Scheduling Policy (Affinity/Anti-affinity) <cce_10_0232>`.

   -  **Toleration**: Using both taints and tolerations allows (not forcibly) the pod to be scheduled to a node with the matching taints, and controls the pod eviction policies after the node where the pod is located is tainted. For details, see :ref:`Taints and Tolerations <cce_10_0728>`.
   -  **Labels and Annotations**: Add labels or annotations for pods using key-value pairs. After entering the key and value, click **Confirm**. For details about how to use and configure labels and annotations, see :ref:`Labels and Annotations <cce_10_0386>`.
   -  **DNS**: Configure a separate DNS policy for the workload. For details, see :ref:`DNS Configuration <cce_10_0365>`.
   -  **Network Configuration**:

      -  Pod ingress/egress bandwidth limitation: You can set ingress/egress bandwidth limitation for pods. For details, see :ref:`Configuring QoS Rate Limiting for Inter-Pod Access <cce_10_0382>`.

#. Click **Create Workload** in the lower right corner.

Using kubectl
-------------

The following procedure uses Nginx as an example to describe how to create a workload using kubectl.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create and edit the **nginx-daemonset.yaml** file. **nginx-daemonset.yaml** is an example file name, and you can change it as required.

   **vi nginx-daemonset.yaml**

   The content of the description file is as follows: The following provides an example. For more information on DaemonSets, see `Kubernetes documents <https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/>`__.

   .. code-block::

      apiVersion: apps/v1
      kind: DaemonSet
      metadata:
        name: nginx-daemonset
        labels:
          app: nginx-daemonset
      spec:
        selector:
          matchLabels:
            app: nginx-daemonset
        template:
          metadata:
            labels:
              app: nginx-daemonset
          spec:
            nodeSelector:                 # Node selection. A pod is created on a node only when the node meets daemon=need.
              daemon: need
            containers:
            - name: nginx-daemonset
              image: nginx:alpine
              resources:
                limits:
                  cpu: 250m
                  memory: 512Mi
                requests:
                  cpu: 250m
                  memory: 512Mi
            imagePullSecrets:
            - name: default-secret

   The **replicas** parameter used in defining a Deployment or StatefulSet does not exist in the above configuration for a DaemonSet, because each node has only one replica. It is fixed.

   DaemonSet only creates pods on nodes with specific labels. In the preceding pod template, **nodeSelector** specifies that a pod is created only on nodes with the **daemon: need** label. If you want to create a pod on each node, delete the label.

#. Create a DaemonSet.

   **kubectl create -f nginx-daemonset.yaml**

   If the following information is displayed, the DaemonSet is being created.

   .. code-block::

      daemonset.apps/nginx-daemonset created

#. Query the DaemonSet status.

   **kubectl get ds**

   .. code-block::

      $ kubectl get ds
      NAME              DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
      nginx-daemonset   1         1         0       1            0           daemon=need     116s

#. If the workload will be accessed through a ClusterIP or NodePort Service, set the corresponding workload access type. For details, see :ref:`Network <cce_10_0020>`.
