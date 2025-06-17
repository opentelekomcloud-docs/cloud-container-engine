:original_name: cce_10_0767.html

.. _cce_10_0767:

Node Pool Affinity
==================

In scenarios such as node pool replacement and rolling node upgrade, an old resource pool needs to be replaced with a new one. To prevent the node pool replacement from affecting services, enable soft affinity to schedule service pods to the new node pool. Soft affinity scheduling tries to schedule newly created pods or rescheduled pods to the new node pool. If the pods cannot be scheduled to the new node pool, for example, due to insufficient resources, the pods can also be scheduled to the old node pool. Since a node pool replacement should not affect services, the node affinity configuration is not declared in service workloads. Use soft affinity in cluster scheduling to schedule pods to new node pools when a pool replacement is triggered.

Volcano aims to soft schedule service pods to specified nodes when node soft affinity is not configured on service workloads.

Scheduling Priority
-------------------

Soft affinity scheduling of a node pool is implemented based on labels in the node pool. Each node in the node pool is scored to select the optimal one for pod scheduling.

The rule is to schedule pods to nodes with specified labels as far as possible.

The scoring formula is as follows:

Node score = Weight x MaxNodeScore x haveLabel

Parameters:

-  **Weight**: weight of the soft affinity add-on in the node pool.
-  **MaxNodeScore**: maximum score (100) of a node.
-  **haveLabel**: whether the labels configured in the add-on are available on a node. If yes, the value is **1**. If no, the value is **0**.

Prerequisites
-------------

-  A cluster of v1.19.16 or later is available. For details, see :ref:`Creating a CCE Standard/Turbo Cluster <cce_10_0028>`.
-  Volcano of v1.11.5 or later has been installed in the cluster. For details, see :ref:`Volcano Scheduler <cce_10_0193>`.

Configuring Soft Affinity Scheduling for Volcano Node Pools
-----------------------------------------------------------

#. Configure labels for affinity scheduling in the node pool.

   a. Log in to the CCE console.
   b. Click the cluster name to access the cluster console. Choose **Nodes** in the navigation pane. In the right pane, click the **Node Pools** tab.
   c. Click **Update** of the target node pool. On the page that is displayed, configure labels in the **Kubernetes Label** area.

#. In the navigation pane, choose **Settings** and click the **Scheduling** tab. Select **Volcano scheduler**, find the expert mode, and click **Try Now**.

#. Configure Volcano scheduler parameters. The following shows a configuration example in JSON format:

   .. code-block::

      ...
          "default_scheduler_conf": {
              "actions": "allocate, backfill, preempt",
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
                              // Enable node pool affinity scheduling.
                              "name": "nodepoolaffinity",
                              // Configure the affinity scheduling weight and labels of the node pool.
                              "arguments": {
                                  "nodepoolaffinity.weight": 10000,
                                  "nodepoolaffinity.label": "nodepool=nodepool1"
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
      ...

#. Click **OK**.
