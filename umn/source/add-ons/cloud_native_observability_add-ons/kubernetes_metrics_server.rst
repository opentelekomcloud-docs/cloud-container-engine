:original_name: cce_10_0205.html

.. _cce_10_0205:

Kubernetes Metrics Server
=========================

From version 1.8 onwards, Kubernetes provides resource usage metrics, such as the container CPU and memory usage, through the Metrics API. These metrics can be directly accessed by users (for example, by using the **kubectl top** command) or used by controllers (for example, Horizontal Pod Autoscaler) in a cluster for decision-making. The specific component is metrics-server, which is used to substitute for Heapster for providing the similar functions. Heapster has been gradually abandoned since v1.11.

metrics-server is an aggregator for monitoring data of core cluster resources. You can quickly install this add-on on the CCE console.

After installing this add-on, you can create HPA policies. For details, see :ref:`Creating an HPA Policy <cce_10_0208>`.

The official community project and documentation are available at https://github.com/kubernetes-sigs/metrics-server.

Installing the Add-on
---------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Add-ons**. Locate **Kubernetes Metrics Server** on the right and click **Install**.
#. On the **Install Add-on** page, configure the specifications as needed.

   -  If you selected **Preset**, you can choose between **Small** or **Large** as needed. The system will automatically set the number of add-on pods and resource quotas according to the preset specifications. You can see the configurations on the console.

      The **smaller specification** lacks HA capabilities, while the **large specification** has them. However, deploying multiple pods requires more compute resources.

   -  If you selected **Custom**, you can adjust the number of pods and resource quotas as needed. High availability is not possible with a single pod. If an error occurs on the node where the add-on pod runs, the add-on will fail.

#. Configure deployment policies for the add-on pods.

   .. note::

      -  Scheduling policies do not take effect on the DaemonSet pods of the add-on.
      -  When configuring multi-AZ deployment or node affinity, ensure that there are nodes meeting the scheduling policy and that resources are sufficient in the cluster. Otherwise, the add-on pods cannot run.

   .. table:: **Table 1** Configurations for add-on scheduling

      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                    |
      +===================================+================================================================================================================================================================================================================================================================================================================================================================================================================================================+
      | Multi-AZ Deployment               | -  **Preferred**: Deployment pods of the add-on will be preferentially scheduled to nodes in different AZs. If all the nodes in the cluster are deployed in the same AZ, the pods will be scheduled to different nodes in that AZ.                                                                                                                                                                                                             |
      |                                   | -  **Equivalent mode**: Deployment pods of the add-on are evenly scheduled to the nodes in the cluster in each AZ. If a new AZ is added, you are advised to increase add-on pods for cross-AZ HA deployment. With the Equivalent multi-AZ deployment, the difference between the number of add-on pods in different AZs will be less than or equal to 1. If resources in one of the AZs are insufficient, pods cannot be scheduled to that AZ. |
      |                                   | -  **Forcible**: Deployment pods of the add-on are forcibly scheduled to nodes in different AZs. There can be at most one pod in each AZ. If nodes in a cluster are not in different AZs, some add-on pods cannot run properly. If a node is faulty, the add-on pods on it may fail to be migrated.                                                                                                                                            |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Node Affinity                     | -  **Not configured**: Node affinity is disabled for the add-on pods.                                                                                                                                                                                                                                                                                                                                                                          |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | -  **Specify node**: Specify the nodes where the add-on pods are deployed. If you do not specify the nodes, the add-on pods will be randomly scheduled based on the default cluster scheduling policy.                                                                                                                                                                                                                                         |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | -  **Specify node pool**: Specify the node pool where the add-on pods are deployed. If you do not specify the node pools, the add-on pods will be randomly scheduled based on the default cluster scheduling policy.                                                                                                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | -  **Customize affinity**: Enter the labels of the nodes where the add-on pods are to be deployed for more flexible scheduling policies. If you do not specify node labels, the add-on pods will be randomly scheduled based on the default cluster scheduling policy.                                                                                                                                                                         |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   |    If multiple custom affinity policies are configured, ensure that there are nodes that meet all the affinity policies in the cluster. Otherwise, the add-on pods cannot run.                                                                                                                                                                                                                                                                 |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Toleration                        | Using both taints and tolerations enables (but does not require) the add-on's Deployment pods to be scheduled on nodes with matching taints, and allows control over pod eviction policies when host nodes are tainted.                                                                                                                                                                                                                        |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | The add-on applies default toleration policies for the **node.kubernetes.io/not-ready** and **node.kubernetes.io/unreachable** taints on pods. The tolerance time window is 60s.                                                                                                                                                                                                                                                               |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | For details, see :ref:`Configuring Tolerance Policies <cce_10_0728>`.                                                                                                                                                                                                                                                                                                                                                                          |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Install**.

Components
----------

.. table:: **Table 2** Add-on components

   +----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
   | Component      | Description                                                                                                                                                                | Resource Type |
   +================+============================================================================================================================================================================+===============+
   | metrics-server | Aggregator for the monitored data of cluster core resources, which is used to collect and aggregate resource usage metrics obtained through the Metrics API in the cluster | Deployment    |
   +----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+

Common Issues
-------------

-  The Kubernetes Metrics Server add-on fails to be installed, and the following error is displayed:

   .. code-block::

      create release failed: create release failed {"error":{"message":"Create release by helm failed:rendered manifests contain a resource that already exists. Unable to continue with install: APIService \"v1beta1.metrics.k8s.io\" in namespace \"\" exists and cannot be imported into the current release: invalid ownership metadata; label validation error: missing key \"app.kubernetes.io/managed-by\": must be set to \"Helm\"; annotation validation error: missing key \"meta.helm.sh/release-name\": must be set to \"cceaddon-metrics-server\"; annotation validation error: missing key \"meta.helm.sh/release-namespace\": must be set to \"kube-system\" or key \"release\" must equal \"cceaddon-metrics-server\": current value is \"cceaddon-prometheus\"","code":"SVCSTG.CCECAM.5000208"}}, 500

   **Solution**

   The possible cause is that the **v1beta1.metrics.k8s.io** APIService already exists in the cluster. This may occur if the Cloud Native Cluster Monitoring add-on was previously installed and its Metrics API feature was enabled. For details, see :ref:`Providing Basic Resource Metrics Through the Metrics API <cce_10_0406__section17830202915211>`.

   Run the following command to delete the APIService object:

   .. code-block::

      kubectl delete APIService v1beta1.metrics.k8s.io

   Reinstall Kubernetes Metrics Server.

Release History
---------------

.. table:: **Table 3** Kubernetes Metrics Server updates

   +-----------------+---------------------------+---------------------------------------------------------------------------------+-----------------------------------------------------------------------------------+
   | Add-on Version  | Supported Cluster Version | New Feature                                                                     | Community Version                                                                 |
   +=================+===========================+=================================================================================+===================================================================================+
   | 1.3.132         | v1.29                     | Supported CCE clusters v1.35.                                                   | `0.8.0 <https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.8.0>`__ |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.30                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.31                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.32                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.33                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.34                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.35                     |                                                                                 |                                                                                   |
   +-----------------+---------------------------+---------------------------------------------------------------------------------+-----------------------------------------------------------------------------------+
   | 1.3.111         | v1.28                     | Supported CCE clusters v1.34.                                                   | `0.6.2 <https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.6.2>`__ |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.29                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.30                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.31                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.32                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.33                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.34                     |                                                                                 |                                                                                   |
   +-----------------+---------------------------+---------------------------------------------------------------------------------+-----------------------------------------------------------------------------------+
   | 1.3.104         | v1.25                     | Supported CCE clusters v1.33.                                                   | `0.6.2 <https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.6.2>`__ |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.27                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.28                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.29                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.30                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.31                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.32                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.33                     |                                                                                 |                                                                                   |
   +-----------------+---------------------------+---------------------------------------------------------------------------------+-----------------------------------------------------------------------------------+
   | 1.3.102         | v1.25                     | Supported CCE clusters v1.32.                                                   | `0.6.2 <https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.6.2>`__ |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.27                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.28                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.29                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.30                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.31                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.32                     |                                                                                 |                                                                                   |
   +-----------------+---------------------------+---------------------------------------------------------------------------------+-----------------------------------------------------------------------------------+
   | 1.3.90          | v1.25                     | Supported CCE clusters v1.31.                                                   | `0.6.2 <https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.6.2>`__ |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.27                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.28                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.29                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.30                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.31                     |                                                                                 |                                                                                   |
   +-----------------+---------------------------+---------------------------------------------------------------------------------+-----------------------------------------------------------------------------------+
   | 1.3.68          | v1.21                     | Supported CCE clusters v1.30.                                                   | `0.6.2 <https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.6.2>`__ |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.23                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.25                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.27                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.28                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.29                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.30                     |                                                                                 |                                                                                   |
   +-----------------+---------------------------+---------------------------------------------------------------------------------+-----------------------------------------------------------------------------------+
   | 1.3.60          | v1.21                     | Supported CCE clusters v1.29.                                                   | `0.6.2 <https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.6.2>`__ |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.23                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.25                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.27                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.28                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.29                     |                                                                                 |                                                                                   |
   +-----------------+---------------------------+---------------------------------------------------------------------------------+-----------------------------------------------------------------------------------+
   | 1.3.39          | v1.21                     | Fixed some issues.                                                              | `0.6.2 <https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.6.2>`__ |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.23                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.25                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.27                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.28                     |                                                                                 |                                                                                   |
   +-----------------+---------------------------+---------------------------------------------------------------------------------+-----------------------------------------------------------------------------------+
   | 1.3.37          | v1.21                     | Supported CCE clusters v1.28.                                                   | `0.6.2 <https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.6.2>`__ |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.23                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.25                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.27                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.28                     |                                                                                 |                                                                                   |
   +-----------------+---------------------------+---------------------------------------------------------------------------------+-----------------------------------------------------------------------------------+
   | 1.3.12          | v1.19                     | None                                                                            | `0.6.2 <https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.6.2>`__ |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.21                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.23                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.25                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.27                     |                                                                                 |                                                                                   |
   +-----------------+---------------------------+---------------------------------------------------------------------------------+-----------------------------------------------------------------------------------+
   | 1.3.6           | v1.19                     | -  Supported anti-affinity scheduling of add-on pods on nodes in different AZs. | `0.6.2 <https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.6.2>`__ |
   |                 |                           | -  The default taint tolerance duration is changed to 60s.                      |                                                                                   |
   |                 | v1.21                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.23                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.25                     |                                                                                 |                                                                                   |
   +-----------------+---------------------------+---------------------------------------------------------------------------------+-----------------------------------------------------------------------------------+
   | 1.3.2           | v1.19                     | Supported CCE clusters v1.25.                                                   | `0.6.2 <https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.6.2>`__ |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.21                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.23                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.25                     |                                                                                 |                                                                                   |
   +-----------------+---------------------------+---------------------------------------------------------------------------------+-----------------------------------------------------------------------------------+
   | 1.2.1           | v1.19                     | Supported CCE clusters v1.23.                                                   | `0.4.4 <https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.4.4>`__ |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.21                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.23                     |                                                                                 |                                                                                   |
   +-----------------+---------------------------+---------------------------------------------------------------------------------+-----------------------------------------------------------------------------------+
   | 1.1.10          | v1.15                     | Supported CCE clusters v1.21.                                                   | `0.4.4 <https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.4.4>`__ |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.17                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.19                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.21                     |                                                                                 |                                                                                   |
   +-----------------+---------------------------+---------------------------------------------------------------------------------+-----------------------------------------------------------------------------------+
   | 1.1.4           | v1.15                     | Unified resource specification configuration unit.                              | `0.4.4 <https://github.com/kubernetes-sigs/metrics-server/releases/tag/v0.4.4>`__ |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.17                     |                                                                                 |                                                                                   |
   |                 |                           |                                                                                 |                                                                                   |
   |                 | v1.19                     |                                                                                 |                                                                                   |
   +-----------------+---------------------------+---------------------------------------------------------------------------------+-----------------------------------------------------------------------------------+
