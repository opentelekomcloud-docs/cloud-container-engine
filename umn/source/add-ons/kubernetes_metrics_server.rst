:original_name: cce_10_0205.html

.. _cce_10_0205:

Kubernetes Metrics Server
=========================

From version 1.8 onwards, Kubernetes provides resource usage metrics, such as the container CPU and memory usage, through the Metrics API. These metrics can be directly accessed by users (for example, by using the **kubectl top** command) or used by controllers (for example, Horizontal Pod Autoscaler) in a cluster for decision-making. The specific component is metrics-server, which is used to substitute for heapster for providing the similar functions. heapster has been gradually abandoned since v1.11.

metrics-server is an aggregator for monitoring data of core cluster resources. You can quickly install this add-on on the CCE console.

After installing this add-on, you can create HPA policies. For details, see :ref:`HPA Policies <cce_10_0208>`.

The official community project and documentation are available at https://github.com/kubernetes-sigs/metrics-server.

Installing the Add-on
---------------------

#. Log in to the CCE console and click the cluster name to access the cluster console. Choose **Add-ons** in the navigation pane, locate **Kubernetes Metrics Server** on the right, and click **Install**.
#. On the **Install Add-on** page, configure the specifications.

   .. table:: **Table 1** metrics-server configuration

      +-----------------------------------+----------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                            |
      +===================================+========================================================================================+
      | Add-on Specifications             | Select **Single**, **Custom**, or **HA** for **Add-on Specifications**.                |
      +-----------------------------------+----------------------------------------------------------------------------------------+
      | Pods                              | Number of pods that will be created to match the selected add-on specifications.       |
      |                                   |                                                                                        |
      |                                   | If you select **Custom**, you can adjust the number of pods as required.               |
      +-----------------------------------+----------------------------------------------------------------------------------------+
      | Containers                        | CPU and memory quotas of the container allowed for the selected add-on specifications. |
      |                                   |                                                                                        |
      |                                   | If you select **Custom**, you can adjust the container specifications as required.     |
      +-----------------------------------+----------------------------------------------------------------------------------------+

#. Configure scheduling policies for the add-on.

   .. note::

      -  Scheduling policies do not take effect on add-on instances of the DaemonSet type.
      -  When configuring multi-AZ deployment or node affinity, ensure that there are nodes meeting the scheduling policy and that resources are sufficient in the cluster. Otherwise, the add-on cannot run.

   .. table:: **Table 2** Configurations for add-on scheduling

      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                    |
      +===================================+================================================================================================================================================================================================================================================================================================================================================================================================================================================+
      | Multi AZ                          | -  **Preferred**: Deployment pods of the add-on will be preferentially scheduled to nodes in different AZs. If all the nodes in the cluster are deployed in the same AZ, the pods will be scheduled to that AZ.                                                                                                                                                                                                                                |
      |                                   | -  **Equivalent mode**: Deployment pods of the add-on are evenly scheduled to the nodes in the cluster in each AZ. If a new AZ is added, you are advised to increase add-on pods for cross-AZ HA deployment. With the Equivalent multi-AZ deployment, the difference between the number of add-on pods in different AZs will be less than or equal to 1. If resources in one of the AZs are insufficient, pods cannot be scheduled to that AZ. |
      |                                   | -  **Required**: Deployment pods of the add-on will be forcibly scheduled to nodes in different AZs. If there are fewer AZs than pods, the extra pods will fail to run.                                                                                                                                                                                                                                                                        |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Node Affinity                     | -  **Not configured**: Node affinity is disabled for the add-on.                                                                                                                                                                                                                                                                                                                                                                               |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | -  **Node Affinity**: Specify the nodes where the add-on is deployed. If you do not specify the nodes, the add-on will be randomly scheduled based on the default cluster scheduling policy.                                                                                                                                                                                                                                                   |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | -  **Specified Node Pool Scheduling**: Specify the node pool where the add-on is deployed. If you do not specify the node pool, the add-on will be randomly scheduled based on the default cluster scheduling policy.                                                                                                                                                                                                                          |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | -  **Custom Policies**: Enter the labels of the nodes where the add-on is to be deployed for more flexible scheduling policies. If you do not specify node labels, the add-on will be randomly scheduled based on the default cluster scheduling policy.                                                                                                                                                                                       |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   |    If multiple custom affinity policies are configured, ensure that there are nodes that meet all the affinity policies in the cluster. Otherwise, the add-on cannot run.                                                                                                                                                                                                                                                                      |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Toleration                        | Using both taints and tolerations allows (not forcibly) the add-on Deployment to be scheduled to a node with the matching taints, and controls the Deployment eviction policies after the node where the Deployment is located is tainted.                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | The add-on adds the default tolerance policy for the **node.kubernetes.io/not-ready** and **node.kubernetes.io/unreachable** taints, respectively. The tolerance time window is 60s.                                                                                                                                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | For details, see :ref:`Taints and Tolerations <cce_10_0728>`.                                                                                                                                                                                                                                                                                                                                                                                  |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Install**.

Components
----------

.. table:: **Table 3** Add-on components

   +----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
   | Component      | Description                                                                                                                                                                | Resource Type |
   +================+============================================================================================================================================================================+===============+
   | metrics-server | Aggregator for the monitored data of cluster core resources, which is used to collect and aggregate resource usage metrics obtained through the Metrics API in the cluster | Deployment    |
   +----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
