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

You must have one cluster available before creating a DaemonSet. For details on how to create a cluster, see :ref:`Creating a CCE Cluster <cce_10_0028>`.

Using the CCE Console
---------------------

#. Log in to the CCE console.

#. Click the cluster name to go to the cluster console, choose **Workloads** in the navigation pane, and click the **Create Workload** in the upper right corner.

#. Set basic information about the workload.

   **Basic Info**

   -  **Workload Type**: Select **DaemonSet**. For details about workload types, see :ref:`Overview <cce_10_0006>`.
   -  **Workload Name**: Enter the name of the workload. Enter 1 to 63 characters starting with a lowercase letter and ending with a letter or digit. Only lowercase letters, digits, and hyphens (-) are allowed.
   -  **Namespace**: Select the namespace of the workload. The default value is **default**. You can also click **Create Namespace** to create one. For details, see :ref:`Creating a Namespace <cce_10_0278>`.
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

   The nodeSelector in the preceding pod template specifies that a pod is created only on the nodes that meet **daemon=need**, as shown in the following figure. If you want to create a pod on each node, delete the label.

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

#. If the workload will be accessed through a ClusterIP or NodePort Service, set the corresponding workload access type. For details, see :ref:`Networking <cce_10_0020>`.
