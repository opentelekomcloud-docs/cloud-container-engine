:original_name: cce_10_0240.html

.. _cce_10_0240:

CCE Advanced HPA
================

cce-hpa-controller is a CCE-developed add-on, which can be used to flexibly scale in or out Deployments based on metrics such as CPU usage and memory usage.

Main Functions
--------------

-  Scaling can be performed based on the percentage of the current number of pods.
-  The minimum scaling step can be set.
-  Different scaling operations can be performed based on the actual metric values.

Notes and Constraints
---------------------

-  This add-on can be installed only in clusters of v1.15 or later.
-  If the add-on version is 1.2.11 or later, the add-ons that can provide metrics API must be installed.

   -  :ref:`Kubernetes Metrics Server <cce_10_0205>`: provides basic resource usage metrics, such as container CPU and memory usage. It is supported by all cluster versions.
   -  :ref:`Cloud Native Cluster Monitoring <cce_10_0406>`: available only in clusters of v1.17 or later.

      -  Auto scaling based on basic resource metrics: Prometheus needs to be registered as a metrics API. For details, see :ref:`Providing Resource Metrics Through the Metrics API <cce_10_0406__section17830202915211>`.
      -  Auto scaling based on custom metrics: Custom metrics need to be aggregated to the Kubernetes API server. For details, see :ref:`Creating an HPA Policy Using Custom Metrics <cce_10_0406__section11927514174016>`.

Installing the Add-on
---------------------

#. Log in to the CCE console and click the cluster name to access the cluster console. Click **Add-ons** in the navigation pane, locate **CCE Advanced HPA** on the right, and click **Install**.
#. On the **Install Add-on** page, configure the specifications.

   .. table:: **Table 1** Add-on configuration

      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
      +===================================+================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================+
      | Add-on Specifications             | Select **Single** or **Custom** for **Add-on Specifications**.                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | .. note::                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   |    A single instance is solely for verification purposes. For commercial situations, you need to choose **Custom** based on the cluster specifications. The add-on specifications are influenced by the total number of containers in clusters and the number of scaling policies. For typical situations, it is recommended that you configure 500m CPU cores and 1,000 MiB of memory for every 5,000 containers. As for scaling policies, 100m CPU cores and 500 MiB of memory should be configured for every 1,000 of them. |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Pods                              | Number of pods that will be created to match the selected add-on specifications.                                                                                                                                                                                                                                                                                                                                                                                                                                               |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | If you select **Custom**, you can adjust the number of pods as required.                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | High availability is not possible with a single pod. If an error occurs on the node where the add-on instance runs, the add-on will fail.                                                                                                                                                                                                                                                                                                                                                                                      |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Containers                        | CPU and memory quotas of the container allowed for the selected add-on specifications.                                                                                                                                                                                                                                                                                                                                                                                                                                         |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | If you select **Custom**, you can adjust the container specifications as required.                                                                                                                                                                                                                                                                                                                                                                                                                                             |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

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
      |                                   | For details, see :ref:`Configuring Tolerance Policies <cce_10_0728>`.                                                                                                                                                                                                                                                                                                                                                                          |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Install**.

Components
----------

.. table:: **Table 3** Add-on components

   +------------------------+--------------------------------------------------------------------------------------------------------------------+---------------+
   | Component              | Description                                                                                                        | Resource Type |
   +========================+====================================================================================================================+===============+
   | customedhpa-controller | CCE auto scaling component, which scales in or out Deployments based on metrics such as CPU usage and memory usage | Deployment    |
   +------------------------+--------------------------------------------------------------------------------------------------------------------+---------------+

Change History
--------------

.. table:: **Table 4** Release history

   +-----------------------+---------------------------+----------------------------------+
   | Add-on Version        | Supported Cluster Version | New Feature                      |
   +=======================+===========================+==================================+
   | 1.4.3                 | v1.21                     | Fixed some issues.               |
   |                       |                           |                                  |
   |                       | v1.23                     |                                  |
   |                       |                           |                                  |
   |                       | v1.25                     |                                  |
   |                       |                           |                                  |
   |                       | v1.27                     |                                  |
   |                       |                           |                                  |
   |                       | v1.28                     |                                  |
   |                       |                           |                                  |
   |                       | v1.29                     |                                  |
   +-----------------------+---------------------------+----------------------------------+
   | 1.3.43                | v1.21                     | Fixed some issues.               |
   |                       |                           |                                  |
   |                       | v1.23                     |                                  |
   |                       |                           |                                  |
   |                       | v1.25                     |                                  |
   |                       |                           |                                  |
   |                       | v1.27                     |                                  |
   |                       |                           |                                  |
   |                       | v1.28                     |                                  |
   +-----------------------+---------------------------+----------------------------------+
   | 1.3.42                | v1.21                     | CCE clusters 1.28 are supported. |
   |                       |                           |                                  |
   |                       | v1.23                     |                                  |
   |                       |                           |                                  |
   |                       | v1.25                     |                                  |
   |                       |                           |                                  |
   |                       | v1.27                     |                                  |
   |                       |                           |                                  |
   |                       | v1.28                     |                                  |
   +-----------------------+---------------------------+----------------------------------+
   | 1.3.14                | v1.19                     | CCE clusters 1.27 are supported. |
   |                       |                           |                                  |
   |                       | v1.21                     |                                  |
   |                       |                           |                                  |
   |                       | v1.23                     |                                  |
   |                       |                           |                                  |
   |                       | v1.25                     |                                  |
   |                       |                           |                                  |
   |                       | v1.27                     |                                  |
   +-----------------------+---------------------------+----------------------------------+
