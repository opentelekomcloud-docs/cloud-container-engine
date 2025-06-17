:original_name: cce_10_0240.html

.. _cce_10_0240:

CCE Advanced HPA
================

The CCE Advanced HPA add-on (formerly cce-hpa-controller) is developed by CCE. It can be used to flexibly scale in or out Deployments based on metrics such as CPU usage and memory usage.

Main Functions
--------------

-  Scaling can be performed based on the percentage of the current number of pods.
-  The minimum scaling step can be set.
-  Different scaling operations can be performed based on the actual metric values.

Notes and Constraints
---------------------

-  If the add-on version is 1.2.11 or later, the add-ons that can provide metrics API must be installed.

   -  :ref:`Kubernetes Metrics Server <cce_10_0205>`: provides basic resource usage metrics, such as container CPU and memory usage. It is supported by all cluster versions.
   -  :ref:`Cloud Native Cluster Monitoring <cce_10_0406>`: available only in clusters of v1.17 or later.

      -  Auto scaling based on basic resource metrics: Prometheus needs to be registered as a metrics API. For details, see :ref:`Providing Basic Resource Metrics Through the Metrics API <cce_10_0406__section17830202915211>`.
      -  Auto scaling based on custom metrics: Custom metrics need to be aggregated to the Kubernetes API server. For details, see :ref:`Creating an HPA Policy Using Custom Metrics <cce_10_0406__section11927514174016>`.

Installing the Add-on
---------------------

#. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Add-ons**, locate **CCE Advanced HPA** on the right, and click **Install**.
#. On the **Install Add-on** page, configure the specifications as needed.

   -  If you selected **Preset**, the add-on specifications will be automatically configured based on the recommended values by CCE. These values are suitable for most scenarios and can be viewed on the console.
   -  If you selected **Custom**, you can modify the number of replicas, CPUs, and memory of each add-on component as required.

      .. note::

         **Replicas**: HA is not possible with just one replica, so one replica is used only for verification. In commercial scenarios, you can configure multiple replicas based on the cluster specifications.

         **CPU Quota** and **Memory Quota**: The resource quotas of a component are affected by how many containers and scaling policies in a cluster. For typical situations, it is recommended that you configure 500m CPU cores and 1,000 MiB of memory for every 5,000 containers in a cluster. As for scaling policies, 100m CPU cores and 500 MiB of memory should be configured for every 1,000 of them.

#. Configure the add-on parameters.

   -  **AHPA Policy**: After this function is enabled, historical monitoring metrics can be used to predict the required number of replicas and scales accordingly. For details, see :ref:`Creating an AHPA Policy <cce_10_0934>`.

      To enable AHPA, make sure to install the Cloud Native Cluster Monitoring add-on and enable the function that reports monitoring data to AOM. For details, see :ref:`Cloud Native Cluster Monitoring <cce_10_0406>`.

#. Configure deployment policies for the add-on pods.

   .. note::

      -  Scheduling policies do not take effect on add-on pods of the DaemonSet type.
      -  When configuring multi-AZ deployment or node affinity, ensure that there are nodes meeting the scheduling policy and that resources are sufficient in the cluster. Otherwise, the add-on cannot run.

   .. table:: **Table 1** Configurations for add-on scheduling

      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                    |
      +===================================+================================================================================================================================================================================================================================================================================================================================================================================================================================================+
      | Multi-AZ Deployment               | -  **Preferred**: Deployment pods of the add-on will be preferentially scheduled to nodes in different AZs. If all the nodes in the cluster are deployed in the same AZ, the pods will be scheduled to different nodes in that AZ.                                                                                                                                                                                                             |
      |                                   | -  **Equivalent mode**: Deployment pods of the add-on are evenly scheduled to the nodes in the cluster in each AZ. If a new AZ is added, you are advised to increase add-on pods for cross-AZ HA deployment. With the Equivalent multi-AZ deployment, the difference between the number of add-on pods in different AZs will be less than or equal to 1. If resources in one of the AZs are insufficient, pods cannot be scheduled to that AZ. |
      |                                   | -  **Forcible**: Deployment pods of the add-on are forcibly scheduled to nodes in different AZs. There can be at most one pod in each AZ. If nodes in a cluster are not in different AZs, some add-on pods cannot run properly. If a node is faulty, add-on pods on it may fail to be migrated.                                                                                                                                                |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Node Affinity                     | -  **Not configured**: Node affinity is disabled for the add-on.                                                                                                                                                                                                                                                                                                                                                                               |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | -  **Specify node**: Specify the nodes where the add-on is deployed. If you do not specify the nodes, the add-on will be randomly scheduled based on the default cluster scheduling policy.                                                                                                                                                                                                                                                    |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | -  **Specify node pool**: Specify the node pool where the add-on is deployed. If you do not specify the node pools, the add-on will be randomly scheduled based on the default cluster scheduling policy.                                                                                                                                                                                                                                      |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | -  **Customize affinity**: Enter the labels of the nodes where the add-on is to be deployed for more flexible scheduling policies. If you do not specify node labels, the add-on will be randomly scheduled based on the default cluster scheduling policy.                                                                                                                                                                                    |
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

.. table:: **Table 2** Add-on components

   +------------------------+--------------------------------------------------------------------------------------------------------------------+---------------+
   | Component              | Description                                                                                                        | Resource Type |
   +========================+====================================================================================================================+===============+
   | customedhpa-controller | CCE auto scaling component, which scales in or out Deployments based on metrics such as CPU usage and memory usage | Deployment    |
   +------------------------+--------------------------------------------------------------------------------------------------------------------+---------------+

Change History
--------------

.. table:: **Table 3** Release history

   +-----------------------+---------------------------+-----------------------------------+
   | Add-on Version        | Supported Cluster Version | New Feature                       |
   +=======================+===========================+===================================+
   | 1.5.24                | v1.25                     | Fixed some issues.                |
   |                       |                           |                                   |
   |                       | v1.27                     |                                   |
   |                       |                           |                                   |
   |                       | v1.28                     |                                   |
   |                       |                           |                                   |
   |                       | v1.29                     |                                   |
   |                       |                           |                                   |
   |                       | v1.30                     |                                   |
   |                       |                           |                                   |
   |                       | v1.31                     |                                   |
   +-----------------------+---------------------------+-----------------------------------+
   | 1.5.3                 | v1.21                     | AHPA is available.                |
   |                       |                           |                                   |
   |                       | v1.23                     |                                   |
   |                       |                           |                                   |
   |                       | v1.25                     |                                   |
   |                       |                           |                                   |
   |                       | v1.27                     |                                   |
   |                       |                           |                                   |
   |                       | v1.28                     |                                   |
   |                       |                           |                                   |
   |                       | v1.29                     |                                   |
   |                       |                           |                                   |
   |                       | v1.30                     |                                   |
   +-----------------------+---------------------------+-----------------------------------+
   | 1.4.3                 | v1.21                     | Fixed some issues.                |
   |                       |                           |                                   |
   |                       | v1.23                     |                                   |
   |                       |                           |                                   |
   |                       | v1.25                     |                                   |
   |                       |                           |                                   |
   |                       | v1.27                     |                                   |
   |                       |                           |                                   |
   |                       | v1.28                     |                                   |
   |                       |                           |                                   |
   |                       | v1.29                     |                                   |
   +-----------------------+---------------------------+-----------------------------------+
   | 1.3.43                | v1.21                     | Fixed some issues.                |
   |                       |                           |                                   |
   |                       | v1.23                     |                                   |
   |                       |                           |                                   |
   |                       | v1.25                     |                                   |
   |                       |                           |                                   |
   |                       | v1.27                     |                                   |
   |                       |                           |                                   |
   |                       | v1.28                     |                                   |
   +-----------------------+---------------------------+-----------------------------------+
   | 1.3.42                | v1.21                     | CCE clusters v1.28 are supported. |
   |                       |                           |                                   |
   |                       | v1.23                     |                                   |
   |                       |                           |                                   |
   |                       | v1.25                     |                                   |
   |                       |                           |                                   |
   |                       | v1.27                     |                                   |
   |                       |                           |                                   |
   |                       | v1.28                     |                                   |
   +-----------------------+---------------------------+-----------------------------------+
   | 1.3.14                | v1.19                     | CCE clusters v1.27 are supported. |
   |                       |                           |                                   |
   |                       | v1.21                     |                                   |
   |                       |                           |                                   |
   |                       | v1.23                     |                                   |
   |                       |                           |                                   |
   |                       | v1.25                     |                                   |
   |                       |                           |                                   |
   |                       | v1.27                     |                                   |
   +-----------------------+---------------------------+-----------------------------------+
