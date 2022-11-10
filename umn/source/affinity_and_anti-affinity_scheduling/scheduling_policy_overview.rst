:original_name: cce_01_0051.html

.. _cce_01_0051:

Scheduling Policy Overview
==========================

Custom Scheduling Policies
--------------------------

You can configure node affinity, workload affinity, and workload anti-affinity in custom scheduling policies.

-  :ref:`Node Affinity <cce_01_0232>`
-  :ref:`Workload Affinity <cce_01_0233>`
-  :ref:`Workload Anti-Affinity <cce_01_0234>`

.. note::

   Custom scheduling policies depend on node labels and pod labels. You can use default labels or customize labels as required.

Simple Scheduling Policies
--------------------------

A simple scheduling policy allows you to configure affinity between workloads and AZs, between workloads and nodes, and between workloads.

-  **Workload-AZ affinity**: Multiple AZ-based scheduling policies (including affinity and anti-affinity policies) can be configured. However, scheduling is performed as long as one of the scheduling policies is met.

   -  **Affinity between workloads and AZs**: :ref:`Workload-AZ Affinity <cce_01_0228>`
   -  **Anti-affinity between workloads and AZs**: :ref:`Workload-AZ Anti-Affinity <cce_01_0229>`

-  **Workload-node affinity**: Multiple node-based scheduling policies (including affinity and anti-affinity scheduling) can be configured. However, scheduling is performed as long as one of the scheduling policies is met. For example, if a cluster contains nodes A, B, and C and two scheduling policies are set (one policy defines node A as an affinity node and the other policy defines node B as an anti-affinity node), then the workload can be scheduled to any node other than B.

   -  **Affinity between workloads and nodes**: :ref:`Workload-Node Affinity <cce_01_0225>`
   -  **Anti-affinity between workloads and nodes**: :ref:`Workload-Node Anti-Affinity <cce_01_0226>`

-  **Workload-workload affinity**: Multiple workload-based scheduling policies can be configured, but the labels in these policies must belong to the same workload.

   -  **Affinity between workloads**: For details, see :ref:`Workload-Workload Affinity <cce_01_0220>`. You can deploy workloads on the same node to reduce consumption of network resources.

      :ref:`Figure 1 <cce_01_0051__fig3017424713>` shows an example of affinity deployment, in which all workloads are deployed on the same node.

      .. _cce_01_0051__fig3017424713:

      .. figure:: /_static/images/en-us_image_0165899095.png
         :alt: **Figure 1** Affinity between workloads

         **Figure 1** Affinity between workloads

   -  **Anti-affinity between workloads**: For details, see :ref:`Workload-Workload Anti-Affinity <cce_01_0227>`. Constraining multiple instances of the same workload from being deployed on the same node reduces the impact of system breakdowns. Anti-affinity deployment is also recommended for workloads that may interfere with each other.

      :ref:`Figure 2 <cce_01_0051__fig1505421971>` shows an example of anti-affinity deployment, in which four workloads are deployed on four different nodes.

      .. _cce_01_0051__fig1505421971:

      .. figure:: /_static/images/en-us_image_0165899282.png
         :alt: **Figure 2** Anti-affinity between workloads

         **Figure 2** Anti-affinity between workloads

.. important::

   When setting workload-workload affinity and workload-node affinity, ensure that the affinity relationships do not contradict each other; otherwise, workload deployment will fail.

   For example, Workload 3 will fail to be deployed when the following conditions are met:

   -  Anti-affinity is configured for Workload 1 and Workload 2. Workload 1 is deployed on **Node A** and Workload 2 is deployed on **Node B**.
   -  Affinity is configured between Workload 2 and Workload 3, but the target node on which Workload 3 is to be deployed is **Node C** or **Node A**.
