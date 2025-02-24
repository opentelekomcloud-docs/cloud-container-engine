:original_name: cce_faq_00445.html

.. _cce_faq_00445:

What Are the Impacts of Changing the Flavor of a Node in a CCE Node Pool?
=========================================================================

Context
-------

After you change the flavor of a node in a CCE node pool on the ECS console and then synchronize the ECS status on the CCE console, the node flavor no longer matches the configurations in the node pool.

Impact
------

When you change the node flavor, it also changes the node parameters such as CPU, memory, and ENI quota (available IP addresses). This can cause the auto scaling settings of the node pool where the node is located to not function as expected.

Assume that the CPU and memory of a node are increased from 2 vCPUs and 4 GiB of memory to 4 vCPUs and 8 GiB of memory.

-  During node pool scale-out, the total number of resources in the node pool may exceed the upper limit of the CPU or memory. Expanding a node pool involves calculating resources based on the node template. However, changing the node flavor on the ECS console can cause inconsistencies with the configurations in the node pool, resulting in inaccurate CPU and memory usage for the cluster.
-  During node pool scale-in, too many CPU or memory resources may be scaled down. If the node with changed flavor is removed, the actual number of CPUs or memory to be scaled down (4 vCPUs and 8 GiB of memory) may be greater than the expected 2 vCPUs and 4 GiB of memory.

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
