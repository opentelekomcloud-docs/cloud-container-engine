:original_name: cce_faq_00445.html

.. _cce_faq_00445:

What Are the Impacts of Changing the Flavor of a Node in a CCE Node Pool?
=========================================================================

Context
-------

After you change the flavor of a node in a CCE node pool on the ECS console and then synchronize the ECS status on the CCE console, the node flavor no longer matches the configurations in the node pool.

Impact
------

-  When you change the node flavor, it also changes the node parameters such as CPU, memory, and ENI quota (available IP addresses). This can cause the auto scaling settings of the node pool where the node is located to not function as expected.

   Assume that the CPU and memory of a node are increased from 2 vCPUs and 4 GiB of memory to 4 vCPUs and 8 GiB of memory.

   -  During node pool scale-out, the total number of resources in the node pool may exceed the upper limit of the CPU or memory. Expanding a node pool involves calculating resources based on the node template. However, changing the node flavor on the ECS console can cause inconsistencies with the configurations in the node pool, resulting in inaccurate CPU and memory usage for the cluster.
   -  During node pool scale-in, too many CPU or memory resources may be scaled down. If the node with changed flavor is removed, the actual number of CPUs or memory to be scaled down (4 vCPUs and 8 GiB of memory) may be greater than the expected 2 vCPUs and 4 GiB of memory.

-  In a **CCE Turbo cluster**, when the flavor of a node in a node pool is changed, the number of network interfaces supported by that node also changes. The maximum number of pods that can run on a node is determined by the smaller value between the number of network interfaces on the node and the maximum number of pods per node configured for the node pool. Because of this, the actual number of pods that can run on a node may not always match expectations.

   -  Case 1: When a node's flavor is changed to a larger flavor, the number of network interfaces on the node may become greater than the maximum number of pods per node defined in the node pool. In this case, scheduling is determined by the smaller of the two values. As a result, the node cannot host more pods than the node pool configuration allows, which may lead to resource waste.

      .. note::

         If you increase the maximum number of pods per node in the node pool while still keeping small-flavor nodes, those small-flavor nodes may fail to run pods due to insufficient network interfaces. It is recommended that the maximum number of pods per node configured for a node pool containing nodes of different flavors be less than or equal to the number of network interfaces of the smallest node flavor in the node pool. If resources are underutilized, you can increase the maximum number of pods per node in the node pool, add enough large-flavor nodes by referring to :ref:`Solution <cce_faq_00445__section143401321952>` and gradually remove small-flavor nodes. The maximum number of network interfaces on a node must always match the maximum number of pods per node defined in the node pool configuration.

   -  Case 2: When a node's flavor is changed to a smaller flavor, the number of network interfaces on the node may become less than the maximum number of pods per node defined in the node pool. In this case, kubelet will still attempt to schedule pods to that node. Once all network interfaces are used up, additional pods cannot run properly.

.. _cce_faq_00445__section143401321952:

Solution
--------

You are not advised to change the flavor of a node in a node pool. Instead, you can update the node pool and add nodes of other flavors to it. The original node will be removed after services are scheduled to the new nodes.

#. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Nodes**.
#. Locate the row containing the target node pool and click **Update**.
#. In the **Specifications** area, select new flavors, click **Next: Confirm**, and submit the request.
#. After the node pool configurations are updated, locate the row containing the target node pool and click **Scaling**.
#. In the window that slides out from the right, select the node flavors to be expanded, configure the number of nodes to be added, and click **OK**.
#. Click the **Nodes** tab, locate the row containing the target node, and choose **More** > **Nodal Drainage** to safely evict the service pods on the node.
#. After the service pods are scheduled to a new node, locate the row containing the target node pool, click **Scaling**, select the flavor of the node to be reduced, configure the number of nodes to be removed, and click **OK**.
