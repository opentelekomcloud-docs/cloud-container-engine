:original_name: cce_10_0766.html

.. _cce_10_0766:

Descheduling
============

Scheduling in a cluster is the process of binding pending pods to nodes, and is performed by a component called kube-scheduler or Volcano Scheduler. The scheduler uses a series of algorithms to compute the optimal node for running pods. However, Kubernetes clusters are dynamic and their state changes over time. For example, if a node needs to be maintained, all pods on the node will be evicted to other nodes. After the maintenance is complete, the evicted pods will not automatically return back to the node because descheduling will not be triggered once a pod is bound to a node. Due to these changes, the load of a cluster may be unbalanced after the cluster runs for a period of time.

CCE has resolved this issue by using Volcano Scheduler to evict pods that do not comply with the configured policy so that pods can be rescheduled. In this way, the cluster load is balanced and resource fragmentation is minimized.

Features
--------

**Load-aware Descheduling**

During Kubernetes cluster management, over-utilized nodes are due to high CPU or memory usage, which affects the stable running of pods on these nodes and increases the probability of node faults. To dynamically balance the resource usage between nodes in a cluster, a cluster resource view is required based on node monitoring metrics. During cluster management, real-time monitoring can be used to detect issues such as high resource usage on a node, node faults, and excessive number of pods on a node so that the system can take measures promptly, for example, by migrating some pods from an over-utilized node to under-utilized nodes.


.. figure:: /_static/images/en-us_image_0000002516079249.png
   :alt: **Figure 1** Load-aware descheduling

   **Figure 1** Load-aware descheduling

When using this add-on, ensure the **highThresholds** value is greater than the **lowThresholds** value. Otherwise, the descheduler cannot work.

-  **Appropriately utilized node**: a node whose resource usage is greater than or equal to 30% and less than or equal to 80%. The resource usage of appropriately utilized nodes is within the expected range.
-  **Over-utilized node**: a node whose resource usage is higher than 80%. Some pods will be evicted from over-utilized nodes to reduce its resource usage to be less than or equal to 80%. The descheduler will schedule the evicted pods to under-utilized nodes.
-  **Under-utilized node**: a node whose resource usage is lower than 30%.

**HighNodeUtilization**

This policy finds nodes that are under-utilized and evicts pods from the nodes in the hope that these pods will be scheduled compactly into fewer nodes. This policy must be used with the bin packing policy of Volcano or the MostAllocated policy of the kube-scheduler. Thresholds can be configured for CPU and memory.

Prerequisites
-------------

-  A cluster of v1.19.16 or later is available. For details, see :ref:`Creating a CCE Standard/Turbo Cluster <cce_10_0028>`.
-  Volcano of v1.11.5 or later has been installed in the cluster. For details, see :ref:`Volcano Scheduler <cce_10_0193>`.

Notes and Constraints
---------------------

-  Pods need to be rescheduled using a scheduler, and no scheduler can label pods or nodes. Therefore, an evicted pod might be rescheduled to the original node.
-  Descheduling does not support anti-affinity between pods. An evicted pod is in anti-affinity relationship with other running pods. Therefore, the scheduler may still schedule the pod back to the node where the pod was evicted from.
-  When configuring load-aware descheduling, you are required to enable load-aware scheduling on Volcano Scheduler. When configuring HighNodeUtilization, you are required to enable bin packing on Volcano Scheduler.

Configuring a Load-aware Descheduling Policy
--------------------------------------------

When configuring a load-aware descheduling policy, do as follows to enable load-aware scheduling on Volcano Scheduler:

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. Choose **Add-ons** in the navigation pane, locate **Volcano Scheduler** on the right, and click **Edit**. In the **Extended Functions** area, enable descheduling and click **OK** to update the add-on configuration.

#. In the navigation pane, choose **Settings**. On the **Scheduling** tab, enable load-aware scheduling. For details, see :ref:`Load-aware Scheduling <cce_10_0789>`.

#. On the **Scheduling** tab, find the expert mode and click **Try Now**.

#. Configure a load-aware descheduling policy. If you are using Volcano v1.11.21 or later, refer to the example JSON parameter settings below. For more parameters, see the JSON example in **Getting Started** on the console.

   .. code-block::

      {
      ...
      "deschedulerPolicy": {
          "profiles": [
            {
              "name": "ProfileName",
              "pluginConfig": [
                {
                  "args": {
                    "ignorePvcPods": true,
                    "nodeFit": true,
                    "priorityThreshold": {
                      "value": 100
                    }
                  },
                  "name": "DefaultEvictor"
                },
                {
                  "args": {
                    "evictableNamespaces": {
                      "exclude": ["kube-system"]
                    },
                    "metrics": {
                      "type": "prometheus_adaptor"
                    },
                    "targetThresholds": {
                      "cpu": 80,
                      "memory": 85
                    },
                    "thresholds": {
                      "cpu": 30,
                      "memory": 30
                    }
                  },
                  "name": "LoadAware"
                }
              ],
              "plugins": {
                "balance": {
                  "enabled": ["LoadAware"]
                }
              }
            }
          ]
        },
        "descheduler_enable": "true",
        "deschedulingInterval": "10m"
      ...
      }

   .. table:: **Table 1** Key parameters of a cluster descheduling policy

      +----------------------+--------------------------------------------------------------------------------------------------+
      | Parameter            | Description                                                                                      |
      +======================+==================================================================================================+
      | deschedulingInterval | Descheduling period.                                                                             |
      +----------------------+--------------------------------------------------------------------------------------------------+
      | deschedulerPolicy    | Cluster descheduling policy. For details, see :ref:`Table 2 <cce_10_0766__table18576915101217>`. |
      +----------------------+--------------------------------------------------------------------------------------------------+

   .. _cce_10_0766__table18576915101217:

   .. table:: **Table 2** deschedulerPolicy parameters

      +---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                             | Description                                                                                                                                                                                                                                                                                       |
      +=======================================+===================================================================================================================================================================================================================================================================================================+
      | profiles.[].plugins.balance.enable.[] | Descheduling policy for a cluster.                                                                                                                                                                                                                                                                |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       | **LoadAware**: a load-aware descheduling policy is used.                                                                                                                                                                                                                                          |
      +---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | profiles.[].pluginConfig.[].name      | Configuration of a load-aware descheduling policy. Options:                                                                                                                                                                                                                                       |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       | -  **DefaultEvictor**: default eviction policy                                                                                                                                                                                                                                                    |
      |                                       | -  **LoadAware**: a load-aware descheduling policy                                                                                                                                                                                                                                                |
      +---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | profiles.[].pluginConfig.[].args      | Descheduling policy configuration of a cluster.                                                                                                                                                                                                                                                   |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       | -  Configurations for the **DefaultEvictor** policy:                                                                                                                                                                                                                                              |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |    -  **ignorePvcPods**: whether PVC pods should be ignored or evicted. Value **true** indicates that the pods are ignored, and value **false** indicates that the pods are evicted. This configuration does not differentiate PVC types (local PVs, SFS, or EVS).                                |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |    -  **nodeFit**: whether to consider the existing scheduling configurations such as node affinity and taint on the node during descheduling. Value **true** indicates that the existing scheduling configurations will be considered, and value **false** indicates that those will be ignored. |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |    -  **priorityThreshold**: priority setting. If the priority of a pod is greater than or equal to the value of this parameter, the pod will not be evicted. Example:                                                                                                                            |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |       .. code-block::                                                                                                                                                                                                                                                                             |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |          {                                                                                                                                                                                                                                                                                        |
      |                                       |            "value": 100                                                                                                                                                                                                                                                                           |
      |                                       |          }                                                                                                                                                                                                                                                                                        |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       | -  Configurations for the **LoadAware** policy:                                                                                                                                                                                                                                                   |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |    -  **evictableNamespaces**: namespaces where the eviction policy takes effect. The default value is the namespaces other than kube-system. Example:                                                                                                                                            |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |       .. code-block::                                                                                                                                                                                                                                                                             |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |          {                                                                                                                                                                                                                                                                                        |
      |                                       |            "exclude": ["kube-system"]                                                                                                                                                                                                                                                             |
      |                                       |          }                                                                                                                                                                                                                                                                                        |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |    -  **metrics**: how monitoring data is obtained. Either the Custom Metrics API (prometheus_adaptor) or Prometheus can be used.                                                                                                                                                                 |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |       For Volcano 1.11.17 and later versions, use Custom Metrics API to obtain monitoring data. The following is an example:                                                                                                                                                                      |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |       .. code-block::                                                                                                                                                                                                                                                                             |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |          {                                                                                                                                                                                                                                                                                        |
      |                                       |            "type": "prometheus_adaptor"                                                                                                                                                                                                                                                           |
      |                                       |          }                                                                                                                                                                                                                                                                                        |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |       For Volcano 1.11.5 to 1.11.16, use Prometheus to obtain monitoring data. You need to enter the IP address of the Prometheus server. The following is an example:                                                                                                                            |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |       .. code-block::                                                                                                                                                                                                                                                                             |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |          {                                                                                                                                                                                                                                                                                        |
      |                                       |            "address": "http://10.247.119.103:9090",                                                                                                                                                                                                                                               |
      |                                       |            "type": "prometheus"                                                                                                                                                                                                                                                                   |
      |                                       |          }                                                                                                                                                                                                                                                                                        |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |    -  **targetThresholds**: threshold for evicting pods from a node. When the CPU or memory usage of a node is greater than the threshold, the pods on the node will be evicted. Example:                                                                                                         |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |       .. code-block::                                                                                                                                                                                                                                                                             |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |          {                                                                                                                                                                                                                                                                                        |
      |                                       |            "cpu": 60,                                                                                                                                                                                                                                                                             |
      |                                       |            "memory": 65                                                                                                                                                                                                                                                                           |
      |                                       |          }                                                                                                                                                                                                                                                                                        |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |    -  **thresholds**: threshold for a node to run pods. When the CPU or memory usage of a node is less than the threshold, the node allows evicted pods to run. Example:                                                                                                                          |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |       .. code-block::                                                                                                                                                                                                                                                                             |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |          {                                                                                                                                                                                                                                                                                        |
      |                                       |            "cpu": 30,                                                                                                                                                                                                                                                                             |
      |                                       |            "memory": 30                                                                                                                                                                                                                                                                           |
      |                                       |          }                                                                                                                                                                                                                                                                                        |
      +---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **OK**.

Configuring a HighNodeUtilization Policy
----------------------------------------

When configuring a HighNodeUtilization policy, do as follows to enable the bin packing policy on Volcano Scheduler:

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. Choose **Add-ons** in the navigation pane, locate **Volcano Scheduler** on the right, and click **Edit**. In the **Extended Functions** area, enable descheduling and click **OK** to update the add-on configuration.

#. In the navigation pane, choose **Settings**. On the **Scheduling** tab, enable bin packing. For details, see :ref:`Bin Packing <cce_10_0773>`.

#. On the **Scheduling** tab, find the expert mode and click **Try Now**.

#. If you are using a resource defragmentation policy, refer to the example JSON parameter settings below. For more parameters, see the JSON example in **Getting Started** on the console.

   .. code-block::

      {
      ...
        "deschedulerPolicy": {
          "profiles": [
            {
              "name": "ProfileName",
              "pluginConfig": [
                {
                  "args": {
                    "ignorePvcPods": true,
                    "nodeFit": true,
                    "priorityThreshold": {
                      "value": 100
                    }
                  },
                  "name": "DefaultEvictor"
                },
                {
                  "args": {
                    "evictableNamespaces": {
                      "exclude": ["kube-system"]
                    },
                    "thresholds": {
                      "cpu": 25,
                      "memory": 25
                    }
                  },
                  "name": "HighNodeUtilization"
                }
              ],
              "plugins": {
                "balance": {
                  "enabled": ["HighNodeUtilization"]
                }
              }
            }
          ]
        },
        "descheduler_enable": "true",
        "deschedulingInterval": "10m"
      ...
      }

   .. table:: **Table 3** Key parameters of a cluster descheduling policy

      +----------------------+--------------------------------------------------------------------------------------------------+
      | Parameter            | Description                                                                                      |
      +======================+==================================================================================================+
      | deschedulingInterval | Descheduling period.                                                                             |
      +----------------------+--------------------------------------------------------------------------------------------------+
      | deschedulerPolicy    | Cluster descheduling policy. For details, see :ref:`Table 4 <cce_10_0766__table66451245121118>`. |
      +----------------------+--------------------------------------------------------------------------------------------------+

   .. _cce_10_0766__table66451245121118:

   .. table:: **Table 4** deschedulerPolicy parameters

      +---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                             | Description                                                                                                                                                                                                                                                                                       |
      +=======================================+===================================================================================================================================================================================================================================================================================================+
      | profiles.[].plugins.balance.enable.[] | Descheduling policy for a cluster.                                                                                                                                                                                                                                                                |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       | **HighNodeUtilization**: the policy for minimizing CPU and memory fragments is used.                                                                                                                                                                                                              |
      +---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | profiles.[].pluginConfig.[].name      | Configuration of a load-aware descheduling policy. Options:                                                                                                                                                                                                                                       |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       | -  **DefaultEvictor**: default eviction policy                                                                                                                                                                                                                                                    |
      |                                       | -  **HighNodeUtilization**: policy for minimizing CPU and memory fragments                                                                                                                                                                                                                        |
      +---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | profiles.[].pluginConfig.[].args      | Descheduling policy configuration of a cluster.                                                                                                                                                                                                                                                   |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       | -  Configurations for the **DefaultEvictor** policy:                                                                                                                                                                                                                                              |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |    -  **ignorePvcPods**: whether PVC pods should be ignored or evicted. Value **true** indicates that the pods are ignored, and value **false** indicates that the pods are evicted. This configuration does not differentiate PVC types (local PVs, SFS, or EVS).                                |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |    -  **nodeFit**: whether to consider the existing scheduling configurations such as node affinity and taint on the node during descheduling. Value **true** indicates that the existing scheduling configurations will be considered, and value **false** indicates that those will be ignored. |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |    -  **priorityThreshold**: priority setting. If the priority of a pod is greater than or equal to the value of this parameter, the pod will not be evicted. Example:                                                                                                                            |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |       .. code-block::                                                                                                                                                                                                                                                                             |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |          {                                                                                                                                                                                                                                                                                        |
      |                                       |            "value": 100                                                                                                                                                                                                                                                                           |
      |                                       |          }                                                                                                                                                                                                                                                                                        |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       | -  Configurations for the **HighNodeUtilization** policy:                                                                                                                                                                                                                                         |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |    -  **evictableNamespaces**: namespaces where the eviction policy takes effect. The default value is the namespaces other than kube-system. Example:                                                                                                                                            |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |       .. code-block::                                                                                                                                                                                                                                                                             |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |          {                                                                                                                                                                                                                                                                                        |
      |                                       |            "exclude": ["kube-system"]                                                                                                                                                                                                                                                             |
      |                                       |          }                                                                                                                                                                                                                                                                                        |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |    -  **thresholds**: threshold for evicting pods from a node. When the CPU or memory usage of a node is less than the threshold, the pods on the node will be evicted. Example:                                                                                                                  |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |       .. code-block::                                                                                                                                                                                                                                                                             |
      |                                       |                                                                                                                                                                                                                                                                                                   |
      |                                       |          {                                                                                                                                                                                                                                                                                        |
      |                                       |            "cpu": 25,                                                                                                                                                                                                                                                                             |
      |                                       |            "memory": 25                                                                                                                                                                                                                                                                           |
      |                                       |          }                                                                                                                                                                                                                                                                                        |
      +---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **OK**.

Use Cases
---------

**HighNodeUtilization**

#. In the navigation pane of the cluster console, choose **Nodes**. Check the node list for nodes with minimal resource allocation.

#. In the navigation pane, choose **Settings** and click the **Scheduling** tab. Find the expert mode and click **Try Now**.

#. Edit Volcano parameters and set both the CPU and memory thresholds to 25. When the CPU and memory usage of a node is less than 25%, pods on the node will be evicted.

   |image1|

#. After the policy takes effect, pods on the node with IP address 192.168.44.152 will be migrated to the node with IP address 192.168.54.65 for minimized resource fragments.

Common Issues
-------------

If an input parameter is incorrect, for example, the entered value is beyond the accepted value range or in an incorrect format, an event will be generated. In this case, modify the parameter setting as prompted.

|image2|

.. |image1| image:: /_static/images/en-us_image_0000002484119280.png
.. |image2| image:: /_static/images/en-us_image_0000002483959314.png
