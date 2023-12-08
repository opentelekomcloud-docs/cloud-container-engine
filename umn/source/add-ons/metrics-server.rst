:original_name: cce_10_0205.html

.. _cce_10_0205:

metrics-server
==============

From version 1.8 onwards, Kubernetes provides resource usage metrics, such as the container CPU and memory usage, through the Metrics API. These metrics can be directly accessed by users (for example, by using the **kubectl top** command) or used by controllers (for example, Horizontal Pod Autoscaler) in a cluster for decision-making. The specific component is metrics-server, which is used to substitute for heapster for providing the similar functions. heapster has been gradually abandoned since v1.11.

metrics-server is an aggregator for monitoring data of core cluster resources. You can quickly install this add-on on the CCE console.

After metrics-server is installed, you can create an HPA policy on the **Workload Scaling** tab page of the **Auto Scaling** page. For details, see :ref:`HPA <cce_10_0208>`.

The official community project and documentation are available at https://github.com/kubernetes-sigs/metrics-server.

Installing the Add-on
---------------------

#. Log in to the CCE console and click the cluster name to access the cluster console. Choose **Add-ons** in the navigation pane, locate **metrics-server** on the right, and click **Install**.
#. On the **Install Add-on** page, configure the specifications.

   .. table:: **Table 1** Add-on configuration

      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                     |
      +===================================+=================================================================================================================================================================================================================+
      | Add-on Specifications             | Select **Single**, **Custom**, or **HA** for **Add-on Specifications**.                                                                                                                                         |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Pods                              | Number of pods that will be created to match the selected add-on specifications.                                                                                                                                |
      |                                   |                                                                                                                                                                                                                 |
      |                                   | If you select **Custom**, you can adjust the number of pods as required.                                                                                                                                        |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Multi-AZ                          | -  **Preferred**: Deployment pods of the add-on will be preferentially scheduled to nodes in different AZs. If all the nodes in the cluster are deployed in the same AZ, the pods will be scheduled to that AZ. |
      |                                   | -  **Required**: Deployment pods of the add-on will be forcibly scheduled to nodes in different AZs. If there are fewer AZs than pods, the extra pods will fail to run.                                         |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Containers                        | CPU and memory quotas of the container allowed for the selected add-on specifications.                                                                                                                          |
      |                                   |                                                                                                                                                                                                                 |
      |                                   | If you select **Custom**, you can adjust the container specifications as required.                                                                                                                              |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Install**.

Components
----------

.. table:: **Table 2** metrics-server components

   +---------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
   | Container Component | Description                                                                                                                                                                | Resource Type |
   +=====================+============================================================================================================================================================================+===============+
   | metrics-server      | Aggregator for the monitored data of cluster core resources, which is used to collect and aggregate resource usage metrics obtained through the Metrics API in the cluster | Deployment    |
   +---------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
