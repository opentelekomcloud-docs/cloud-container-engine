:original_name: cce_10_0193.html

.. _cce_10_0193:

volcano
=======

Introduction
------------

Volcano is a batch processing platform based on Kubernetes. It provides a series of features required by machine learning, deep learning, bioinformatics, genomics, and other big data applications, as a powerful supplement to Kubernetes capabilities.

Volcano provides general-purpose, high-performance computing capabilities, such as job scheduling engine, heterogeneous chip management, and job running management, serving end users through computing frameworks for different industries, such as AI, big data, gene sequencing, and rendering. (Volcano has been open-sourced in GitHub.)

Volcano provides job scheduling, job management, and queue management for computing applications. Its main features are as follows:

-  Diverse computing frameworks, such as TensorFlow, MPI, and Spark, can run on Kubernetes in containers. Common APIs for batch computing jobs through CRD, various plug-ins, and advanced job lifecycle management are provided.
-  Advanced scheduling capabilities are provided for batch computing and high-performance computing scenarios, including group scheduling, preemptive priority scheduling, packing, resource reservation, and task topology.
-  Queues can be effectively managed for scheduling jobs. Complex job scheduling capabilities such as queue priority and multi-level queues are supported.

Open source community: https://github.com/volcano-sh/volcano

Installing the Add-on
---------------------

#. Log in to the CCE console, click the cluster name, and access the cluster console. Choose **Add-ons** in the navigation pane, locate **volcano** on the right, and click **Install**.

#. Select **Standalone**, **Custom**, or **HA** for **Add-on Specifications**.

   If you select **Custom**, the recommended values of **volcano-controller** and **volcano-scheduler** are as follows:

   -  If the number of nodes is less than 100, retain the default configuration. That is, the CPU request value is **500m**, and the limit value is **2000m**. The memory request value is **500Mi**, and the limit value is **2000Mi**.
   -  If the number of nodes is greater than 100, increase the CPU request value by **500m** and the memory request value by **1000Mi** each time 100 nodes (10000 pods) are added. You are advised to increase the CPU limit value by **1500m** and the memory limit by **1000Mi**.

      .. table:: **Table 1** Recommended values for volcano-controller and volcano-scheduler

         +--------------------+----------------+--------------+--------------------+------------------+
         | Number of Node/Pod | CPU Request(m) | CPU Limit(m) | Memory Request(Mi) | Memory Limit(Mi) |
         +====================+================+==============+====================+==================+
         | 50/5k              | 500            | 2000         | 500                | 2000             |
         +--------------------+----------------+--------------+--------------------+------------------+
         | 100/1w             | 1000           | 2500         | 1500               | 2500             |
         +--------------------+----------------+--------------+--------------------+------------------+
         | 200/2w             | 1500           | 3000         | 2500               | 3500             |
         +--------------------+----------------+--------------+--------------------+------------------+
         | 300/3w             | 2000           | 3500         | 3500               | 4500             |
         +--------------------+----------------+--------------+--------------------+------------------+
         | 400/4w             | 2500           | 4000         | 4500               | 5500             |
         +--------------------+----------------+--------------+--------------------+------------------+

#. Select whether to deploy the add-on pods across multiple AZs.

   -  **Preferred**: Deployment pods of the add-on are preferentially scheduled to nodes in different AZs. If the nodes in the cluster do not meet the requirements of multiple AZs, the pods are scheduled to a single AZ.
   -  **Required**: Deployment pods of the add-on are forcibly scheduled to nodes in different AZs. If the nodes in the cluster do not meet the requirements of multiple AZs, not all pods can run.

#. Parameters of the volcano default scheduler. For details, see :ref:`Table 2 <cce_10_0193__table562185146>`.

   .. code-block::

      colocation_enable: ''
      default_scheduler_conf:
        actions: 'allocate, backfill'
        tiers:
          - plugins:
              - name: 'priority'
              - name: 'gang'
              - name: 'conformance'
          - plugins:
              - name: 'drf'
              - name: 'predicates'
              - name: 'nodeorder'
          - plugins:
              - name: 'cce-gpu-topology-predicate'
              - name: 'cce-gpu-topology-priority'
              - name: 'cce-gpu'
          - plugins:
              - name: 'nodelocalvolume'
              - name: 'nodeemptydirvolume'
              - name: 'nodeCSIscheduling'
              - name: 'networkresource'

   .. _cce_10_0193__table562185146:

   .. table:: **Table 2** Volcano Plugins

      +----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------+
      | Add-on                     | Function                                                                                                                                                                                                                    | Description                                                                                                              | Demonstration                                               |
      +============================+=============================================================================================================================================================================================================================+==========================================================================================================================+=============================================================+
      | binpack                    | Schedules pods to nodes with high resource utilization to reduce resource fragments.                                                                                                                                        | -  **binpack.weight**: Weight of the binpack plugin.                                                                     | .. code-block::                                             |
      |                            |                                                                                                                                                                                                                             | -  **binpack.cpu**: ratio of CPU resources to all resources. Defaults to **1**.                                          |                                                             |
      |                            |                                                                                                                                                                                                                             | -  **binpack.memory**: Ratio of memory resources to all resources. Defaults to **1**.                                    |    - plugins:                                               |
      |                            |                                                                                                                                                                                                                             | -  **binpack.resources**: resource type.                                                                                 |      - name: binpack                                        |
      |                            |                                                                                                                                                                                                                             |                                                                                                                          |        arguments:                                           |
      |                            |                                                                                                                                                                                                                             |                                                                                                                          |          binpack.weight: 10                                 |
      |                            |                                                                                                                                                                                                                             |                                                                                                                          |          binpack.cpu: 1                                     |
      |                            |                                                                                                                                                                                                                             |                                                                                                                          |          binpack.memory: 1                                  |
      |                            |                                                                                                                                                                                                                             |                                                                                                                          |          binpack.resources: nvidia.com/gpu, example.com/foo |
      |                            |                                                                                                                                                                                                                             |                                                                                                                          |          binpack.resources.nvidia.com/gpu: 2                |
      |                            |                                                                                                                                                                                                                             |                                                                                                                          |          binpack.resources.example.com/foo: 3               |
      +----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------+
      | conformance                | The conformance plugin considers that the tasks in namespace **kube-system** have a higher priority. These tasks will not be preempted.                                                                                     | ``-``                                                                                                                    | ``-``                                                       |
      +----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------+
      | gang                       | The gang plugin considers a group of pods as a whole to allocate resources.                                                                                                                                                 | ``-``                                                                                                                    | ``-``                                                       |
      +----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------+
      | priority                   | The priority plugin schedules pods based on the custom workload priority.                                                                                                                                                   | ``-``                                                                                                                    | ``-``                                                       |
      +----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------+
      | overcommit                 | Resources in a cluster are scheduled after being accumulated in a certain multiple to improve the workload enqueuing efficiency. If all workloads are Deployments, remove this plugin or set the raising factor to **2.0**. | **overcommit-factor**: Raising factor. Defaults to **1.2**.                                                              | .. code-block::                                             |
      |                            |                                                                                                                                                                                                                             |                                                                                                                          |                                                             |
      |                            |                                                                                                                                                                                                                             |                                                                                                                          |    - plugins:                                               |
      |                            |                                                                                                                                                                                                                             |                                                                                                                          |      - name: overcommit                                     |
      |                            |                                                                                                                                                                                                                             |                                                                                                                          |        arguments:                                           |
      |                            |                                                                                                                                                                                                                             |                                                                                                                          |          overcommit-factor: 2.0                             |
      +----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------+
      | drf                        | The DRF plugin schedules resources based on the container group Domaint Resource. The smallest Domaint Resource would be selected for priority scheduling.                                                                  | ``-``                                                                                                                    | ``-``                                                       |
      +----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------+
      | predicates                 | Determines whether a task is bound to a node by using a series of evaluation algorithms, such as node/pod affinity, taint tolerance, node port repetition, volume limits, and volume zone matching.                         | ``-``                                                                                                                    | ``-``                                                       |
      +----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------+
      | nodeorder                  | The nodeorder plugin scores all nodes for a task by using a series of scoring algorithms.                                                                                                                                   | -  **nodeaffinity.weight**: Pods are scheduled based on the node affinity. Defaults to **1**.                            | .. code-block::                                             |
      |                            |                                                                                                                                                                                                                             | -  **podaffinity.weight**: Pods are scheduled based on the pod affinity. Defaults to **1**.                              |                                                             |
      |                            |                                                                                                                                                                                                                             | -  **leastrequested.weight**: Pods are scheduled to the node with the least resources. Defaults to **1**.                |    - plugins:                                               |
      |                            |                                                                                                                                                                                                                             | -  **balancedresource.weight**: Pods are scheduled to the node with balanced resource. Defaults to **1**.                |      - name: nodeorder                                      |
      |                            |                                                                                                                                                                                                                             | -  **mostrequested.weight**: Pods are scheduled to the node with the most requested resources. Defaults to **0**.        |        arguments:                                           |
      |                            |                                                                                                                                                                                                                             | -  **tainttoleration.weight**: Pods are scheduled to the node with a high taint tolerance. Defaults to **1**.            |          leastrequested.weight: 1                           |
      |                            |                                                                                                                                                                                                                             | -  **imagelocality.weight**: Pods are scheduled to the node where the required images exist. Defaults to **1**.          |          mostrequested.weight: 0                            |
      |                            |                                                                                                                                                                                                                             | -  **selectorspread.weight**: Pods are evenly scheduled to different nodes. Defaults to **0**.                           |          nodeaffinity.weight: 1                             |
      |                            |                                                                                                                                                                                                                             | -  **volumebinding.weight**: Pods are scheduled to the node with the local PV delayed binding policy. Defaults to **1**. |          podaffinity.weight: 1                              |
      |                            |                                                                                                                                                                                                                             | -  **podtopologyspread.weight**: Pods are scheduled based on the pod topology. Defaults to **2**.                        |          balancedresource.weight: 1                         |
      |                            |                                                                                                                                                                                                                             |                                                                                                                          |          tainttoleration.weight: 1                          |
      |                            |                                                                                                                                                                                                                             |                                                                                                                          |          imagelocality.weight: 1                            |
      |                            |                                                                                                                                                                                                                             |                                                                                                                          |          volumebinding.weight: 1                            |
      |                            |                                                                                                                                                                                                                             |                                                                                                                          |          podtopologyspread.weight: 2                        |
      +----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------+
      | cce-gpu-topology-predicate | GPU-topology scheduling preselection algorithm                                                                                                                                                                              | ``-``                                                                                                                    | ``-``                                                       |
      +----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------+
      | cce-gpu-topology-priority  | GPU-topology scheduling priority algorithm                                                                                                                                                                                  | ``-``                                                                                                                    | ``-``                                                       |
      +----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------+
      | cce-gpu                    | Works with the gpu add-on of CCE to support GPU resource allocation and decimal GPU configuration.                                                                                                                          | ``-``                                                                                                                    | ``-``                                                       |
      +----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------+
      | numaaware                  | NUMA topology scheduling                                                                                                                                                                                                    | weight: Weight of the numa-aware plugin.                                                                                 | ``-``                                                       |
      +----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------+
      | networkresource            | The ENI requirement node can be preselected and filtered. The parameters are transferred by CCE and do not need to be manually configured.                                                                                  | NetworkType: Network type (eni or vpc-router).                                                                           | ``-``                                                       |
      +----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------+
      | nodelocalvolume            | The nodelocalvolume plugin filters out nodes that do not meet local volume requirements can be filtered out.                                                                                                                | ``-``                                                                                                                    | ``-``                                                       |
      +----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------+
      | nodeemptydirvolume         | The nodeemptydirvolume plugin filters out nodes that do not meet the emptyDir requirements.                                                                                                                                 | ``-``                                                                                                                    | ``-``                                                       |
      +----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------+
      | nodeCSIscheduling          | The nodeCSIscheduling plugin filters out nodes that have the everest component exception.                                                                                                                                   | ``-``                                                                                                                    | ``-``                                                       |
      +----------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------+

#. Click **Install**.

Modifying the volcano-scheduler Configuration Using the Console
---------------------------------------------------------------

Volcano allows you to configure the scheduler during installation, upgrade, and editing. The configuration will be synchronized to volcano-scheduler-configmap.

This section describes how to configure the volcano scheduler.

.. note::

   Only Volcano of v1.7.1 and later support this function. On the new plug-in page, options such as **plugins.eas_service** and **resource_exporter_enable** are replaced by **default_scheduler_conf**.

Log in to the CCE console and access the cluster console. Choose **Add-ons** in the navigation pane. On the right of the page, locate **volcano** and click **Install** or **Upgrade**. In the **Parameters** area, configure the volcano scheduler parameters.

-  Using **resource_exporter**:

   .. code-block::

      {
          "ca_cert": "",
          "default_scheduler_conf": {
              "actions": "allocate, backfill",
              "tiers": [
                  {
                      "plugins": [
                          {
                              "name": "priority"
                          },
                          {
                              "name": "gang"
                          },
                          {
                              "name": "conformance"
                          }
                      ]
                  },
                  {
                      "plugins": [
                          {
                              "name": "drf"
                          },
                          {
                              "name": "predicates"
                          },
                          {
                              "name": "nodeorder"
                          }
                      ]
                  },
                  {
                      "plugins": [
                          {
                              "name": "cce-gpu-topology-predicate"
                          },
                          {
                              "name": "cce-gpu-topology-priority"
                          },
                          {
                              "name": "cce-gpu"
                          },
                          {
                              "name": "numa-aware" # add this also enable resource_exporter
                          }
                      ]
                  },
                  {
                      "plugins": [
                          {
                              "name": "nodelocalvolume"
                          },
                          {
                              "name": "nodeemptydirvolume"
                          },
                          {
                              "name": "nodeCSIscheduling"
                          },
                          {
                              "name": "networkresource"
                          }
                      ]
                  }
              ]
          },
          "server_cert": "",
          "server_key": ""
      }

   After this function is enabled, you can use the functions of the numa-aware plug-in and resource_exporter at the same time.

-  Using **eas_service**:

   .. code-block::

      {
          "ca_cert": "",
          "default_scheduler_conf": {
              "actions": "allocate, backfill",
              "tiers": [
                  {
                      "plugins": [
                          {
                              "name": "priority"
                          },
                          {
                              "name": "gang"
                          },
                          {
                              "name": "conformance"
                          }
                      ]
                  },
                  {
                      "plugins": [
                          {
                              "name": "drf"
                          },
                          {
                              "name": "predicates"
                          },
                          {
                              "name": "nodeorder"
                          }
                      ]
                  },
                  {
                      "plugins": [
                          {
                              "name": "cce-gpu-topology-predicate"
                          },
                          {
                              "name": "cce-gpu-topology-priority"
                          },
                          {
                              "name": "cce-gpu"
                          },
                          {
                              "name": "eas",
                              "custom": {
                                  "availability_zone_id": "",
                                  "driver_id": "",
                                  "endpoint": "",
                                  "flavor_id": "",
                                  "network_type": "",
                                  "network_virtual_subnet_id": "",
                                  "pool_id": "",
                                  "project_id": "",
                                  "secret_name": "eas-service-secret"
                              }
                          }
                      ]
                  },
                  {
                      "plugins": [
                          {
                              "name": "nodelocalvolume"
                          },
                          {
                              "name": "nodeemptydirvolume"
                          },
                          {
                              "name": "nodeCSIscheduling"
                          },
                          {
                              "name": "networkresource"
                          }
                      ]
                  }
              ]
          },
          "server_cert": "",
          "server_key": ""
      }

-  Using **ief**:

   .. code-block::

      {
          "ca_cert": "",
          "default_scheduler_conf": {
              "actions": "allocate, backfill",
              "tiers": [
                  {
                      "plugins": [
                          {
                              "name": "priority"
                          },
                          {
                              "name": "gang"
                          },
                          {
                              "name": "conformance"
                          }
                      ]
                  },
                  {
                      "plugins": [
                          {
                              "name": "drf"
                          },
                          {
                              "name": "predicates"
                          },
                          {
                              "name": "nodeorder"
                          }
                      ]
                  },
                  {
                      "plugins": [
                          {
                              "name": "cce-gpu-topology-predicate"
                          },
                          {
                              "name": "cce-gpu-topology-priority"
                          },
                          {
                              "name": "cce-gpu"
                          },
                          {
                              "name": "ief",
                              "enableBestNode": true
                          }
                      ]
                  },
                  {
                      "plugins": [
                          {
                              "name": "nodelocalvolume"
                          },
                          {
                              "name": "nodeemptydirvolume"
                          },
                          {
                              "name": "nodeCSIscheduling"
                          },
                          {
                              "name": "networkresource"
                          }
                      ]
                  }
              ]
          },
          "server_cert": "",
          "server_key": ""
      }

Retaining the Original volcano-scheduler-configmap Configuration
----------------------------------------------------------------

If you want to use the original configuration after the plug-in is upgraded, perform the following steps:

#. Check and back up the original volcano-scheduler-configmap configuration.

   Example:

   .. code-block::

      # kubectl edit cm volcano-scheduler-configmap -n kube-system
      apiVersion: v1
      data:
        default-scheduler.conf: |-
          actions: "enqueue, allocate, backfill"
          tiers:
          - plugins:
            - name: priority
            - name: gang
            - name: conformance
          - plugins:
            - name: drf
            - name: predicates
            - name: nodeorder
            - name: binpack
              arguments:
                binpack.cpu: 100
                binpack.weight: 10
                binpack.resources: nvidia.com/gpu
                binpack.resources.nvidia.com/gpu: 10000
          - plugins:
            - name: cce-gpu-topology-predicate
            - name: cce-gpu-topology-priority
            - name: cce-gpu
          - plugins:
            - name: nodelocalvolume
            - name: nodeemptydirvolume
            - name: nodeCSIscheduling
            - name: networkresource

#. Enter the customized content in the **Parameters** on the console.

   .. code-block::

      {
          "ca_cert": "",
          "default_scheduler_conf": {
              "actions": "enqueue, allocate, backfill",
              "tiers": [
                  {
                      "plugins": [
                          {
                              "name": "priority"
                          },
                          {
                              "name": "gang"
                          },
                          {
                              "name": "conformance"
                          }
                      ]
                  },
                  {
                      "plugins": [
                          {
                              "name": "drf"
                          },
                          {
                              "name": "predicates"
                          },
                          {
                              "name": "nodeorder"
                          },
                          {
                              "name": "binpack",
                              "arguments": {
                                  "binpack.cpu": 100,
                                  "binpack.weight": 10,
                                  "binpack.resources": "nvidia.com/gpu",
                                  "binpack.resources.nvidia.com/gpu": 10000
                              }
                          }
                      ]
                  },
                  {
                      "plugins": [
                          {
                              "name": "cce-gpu-topology-predicate"
                          },
                          {
                              "name": "cce-gpu-topology-priority"
                          },
                          {
                              "name": "cce-gpu"
                          }
                      ]
                  },
                  {
                      "plugins": [
                          {
                              "name": "nodelocalvolume"
                          },
                          {
                              "name": "nodeemptydirvolume"
                          },
                          {
                              "name": "nodeCSIscheduling"
                          },
                          {
                              "name": "networkresource"
                          }
                      ]
                  }
              ]
          },
          "server_cert": "",
          "server_key": ""
      }

   .. note::

      When this function is used, the original content in volcano-scheduler-configmap will be overwritten. Therefore, you must check whether volcano-scheduler-configmap has been modified during the upgrade. If yes, synchronize the modification to the upgrade page.
