:original_name: cce_10_0649.html

.. _cce_10_0649:

Priorities for Scaling Node Pools
=================================

Prerequisites
-------------

To use node flavor priorities, the Autoscaler version must be 1.19.35, 1.21.28, 1.23.30, 1.25.20, or later. To balance load among AZs, the version must be 1.23.122, 1.25.117, 1.27.85, 1.28.52, or later.

Elastic Capacity Expansion Policies
-----------------------------------

Node pools are scaled according to their priorities and flavor priorities.

|image1|

#. Predictive flavor filtering:

   -  The predictive algorithm chooses proper flavors that meet the scheduling needs of pending pods from all node pools.
   -  When pods are scheduled, several factors are taken into account. These include checking if the node resources meet the requested resources of the pods, and verifying if the nodeSelector, nodeAffinity, and taints meet the conditions for pod scheduling.
   -  If certain node pool flavors experience scale-out failures, for example, due to insufficient resources, they will enter a 5-minute cooldown period. During this time, the scale-out algorithm will automatically exclude them from being considered.

#. Node pool sorting by priority

   Node pools are assigned priorities and sorted accordingly. The node pool with the highest priority is preferentially selected.

#. Flavor selection by priority

   When multiple node pools have the same highest priority, the flavor with the highest priority is selected according to the following rules:

   -  The flavor with the highest priority in each node pool is selected.
   -  If multiple flavors have the same priority, choose the one that requires the least volume of resources to meet the pod scheduling requirements.
   -  If multiple flavors require the minimum volume of resources, choose one based on a balanced distribution among AZs.

#. Troubleshooting if resources are insufficient or a creation failed

   If the preferred flavor is unavailable due to insufficient quota in the AZ, CCE will try to use the next priority flavor in the node pool, and the original instance will enter a 5-minute cooldown period.

   If none of the flavors in a node pool can be used to create instances, CCE will try to use the next priority node pool to create instances.

Manual Capacity Expansion Policies
----------------------------------

When manually scaling out a node pool, you can select a specified flavor. If the resources of the selected flavor are insufficient or the quota is insufficient, the scale-out will fail.

Configuring Priorities
----------------------

For details about how to configure the priorities of node pool flavors, see :ref:`Configuring an Auto Scaling Policy for a Cluster <cce_10_0209__section6521214191020>`.

.. |image1| image:: /_static/images/en-us_image_0000002218820038.png
