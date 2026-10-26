:original_name: cce_10_0348.html

.. _cce_10_0348:

Maximum Number of Pods That Can Be Created on a Node
====================================================

.. _cce_10_0348__section103406569524:

Calculation of the Maximum Number of Pods on a Node
---------------------------------------------------

The maximum number of pods that can be created on a node is calculated based on the cluster type.

+---------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Network Model                                     | Maximum Number of Pods on a Node                                                                                                                                                                                             | Recommended Configuration                                                                                                                                                                                                                                  |
+===================================================+==============================================================================================================================================================================================================================+============================================================================================================================================================================================================================================================+
| Tunnel network                                    | :ref:`Maximum number of pods on a node <cce_10_0348__section16296174054019>`                                                                                                                                                 | None                                                                                                                                                                                                                                                       |
+---------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| VPC network                                       | The smaller value between the :ref:`Maximum Number of Pods on a Node <cce_10_0348__section16296174054019>` and :ref:`Number of Reserved Pod IP Addresses Per Node <cce_10_0348__section10770192193714>`                      | To ensure that new pods run smoothly on a node, verify that the maximum number of pods on the node does not exceed the number of allocatable pod IP addresses. If the node lacks sufficient pod IP addresses, new pods will not function properly.         |
+---------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Cloud Native Network 2.0 (for CCE Turbo clusters) | The smaller value between the :ref:`maximum number of pods on a node <cce_10_0348__section16296174054019>` and the :ref:`number of network interfaces on a node in a CCE Turbo cluster <cce_10_0348__section15702175115573>` | To ensure new pods run smoothly on a node, make sure that the number of pods on the node does not exceed the number of network interfaces on it. If there are not enough network interfaces available on the node, the new pods may not function properly. |
+---------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_10_0348__section10770192193714:

Number of Reserved Pod IP Addresses Per Node
--------------------------------------------

The number of allocatable pod IP addresses on a node is the maximum number of IP addresses that can be allocated to pods on that node. When creating a cluster in the VPC network model, follow the and specify the number of pod IP addresses that each node can allocate using alpha.cce/fixPoolMask.

The maximum number of pods that can be created on a node is determined by the number of pod IP addresses available for allocation. In a :ref:`containerized environment <cce_10_0348__li13739132619599>`, each pod requires its own unique IP address. If the node runs out of reserved pod IP addresses, new pods cannot be created. If **hostNetwork: true** is configured in the YAML file, pods will use the :ref:`host network <cce_10_0348__li13752132911597>` instead of the reserved pod IP addresses. For details, see :ref:`Pod IP Address Allocation Differences Between the Container Network and Host Network <cce_10_0348__section12428143711548>`.

By default, each node in a cluster is assigned a CIDR block from which pod IP addresses are allocated. The usable number of IP addresses for pods within this block is typically the total number of addresses in the CIDR block minus three reserved addresses (including the network address, gateway address, and broadcast address).

.. _cce_10_0348__section16296174054019:

Maximum Number of Pods on a Node
--------------------------------

When creating a node, you can configure the maximum number of pods (**Max. Pods**) that can be created on the node. This parameter is a configuration item of kubelet and determines the maximum number of pods that can be created by kubelet.

.. important::

   For nodes in the default node pool (**DefaultPool**), the maximum number of pods cannot be changed after the nodes are created.

   After a node in a custom node pool is created, you can modify **Max. Pods** in the node pool configuration to change the maximum number of pods on the node.

:ref:`Table 1 <cce_10_0348__table1710310156526>` lists the default maximum number of pods on a node based on node specifications.

.. _cce_10_0348__table1710310156526:

.. table:: **Table 1** Default maximum number of pods on a node

   =============== =========
   Memory (GiB)    Max. Pods
   =============== =========
   4 GiB           20
   8 GiB           40
   16 GiB          60
   32 GiB          80
   64 GiB or above 110
   =============== =========

.. _cce_10_0348__section15702175115573:

Number of Node Network Interfaces (Available Only for CCE Turbo Clusters)
-------------------------------------------------------------------------

In a CCE Turbo cluster, ECS nodes use supplementary network interfaces. The maximum number of pods that can be created on a node depends on the number of network interfaces that can be used by the node.

.. _cce_10_0348__section12428143711548:

Pod IP Address Allocation Differences Between the Container Network and Host Network
------------------------------------------------------------------------------------

When creating a pod, you can select the container network or host network for the pod.

-  .. _cce_10_0348__li13739132619599:

   Container network (default): Each pod is assigned an IP address from the container network by the cluster networking add-ons.

-  .. _cce_10_0348__li13752132911597:

   Host network: Pods with **hostNetwork: true** configured directly use the network of the host. After the configuration, the pods use the ports on the host and their IP address is identical to that of the host, without relying on the IP addresses of the container network. When using a host network, avoid conflicts between the pod ports and service ports on the host. Use a host network only if a particular application requires access to a specific port on the host.

Increasing Pod Capacity
-----------------------

+---------------------------------------------------------------+---------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Solution                                                      | Applicable Cluster                                | Operation                                                                                                                                                                                                                                                                                                       | Impact                                                                                                                                                                    |
+===============================================================+===================================================+=================================================================================================================================================================================================================================================================================================================+===========================================================================================================================================================================+
| Adding nodes (recommended)                                    | All clusters                                      | Add nodes to increase the number of available pods.                                                                                                                                                                                                                                                             | **Services are not affected.** However, if the cluster is over-provisioned, performance may degrade. Plan the cluster size properly.                                      |
|                                                               |                                                   |                                                                                                                                                                                                                                                                                                                 |                                                                                                                                                                           |
|                                                               |                                                   |                                                                                                                                                                                                                                                                                                                 | Add nodes with higher specifications, gradually migrate services to the new nodes, and scale in the old nodes.                                                            |
+---------------------------------------------------------------+---------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Increasing the maximum pod count per node                     | All clusters                                      | For nodes in the default node pool, reset the nodes and reconfigure the maximum pod count per node. For nodes in a custom node pool, update the **Maximum Pods Managed by kubelet** setting in the node pool configuration.                                                                                     | **Node reset or kubelet restart may cause brief service interruption.**                                                                                                   |
|                                                               |                                                   |                                                                                                                                                                                                                                                                                                                 |                                                                                                                                                                           |
|                                                               |                                                   |                                                                                                                                                                                                                                                                                                                 | For **tunnel network** clusters, this method directly increases the number of available pods. For other types of clusters, additional parameters must also be considered. |
+---------------------------------------------------------------+---------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Rebuilding the cluster and replanning pod CIDR blocks         | VPC network clusters                              | For VPC-network clusters, adjusting the :ref:`number of pod IP addresses reserved per node <cce_10_0348__section10770192193714>` requires cluster rebuild.                                                                                                                                                      | **Services must be rebuilt.**                                                                                                                                             |
+---------------------------------------------------------------+---------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Upgrading node flavors to increase network interface capacity | CCE Turbo clusters using Cloud Native Network 2.0 | If the maximum pod count is limited by the number of network interfaces on a node, upgrade the node flavor to increase capacity.                                                                                                                                                                                | Changing a node flavor causes brief service interruption.                                                                                                                 |
|                                                               |                                                   |                                                                                                                                                                                                                                                                                                                 |                                                                                                                                                                           |
|                                                               |                                                   | If the number of network interfaces supported by a node exceeds the :ref:`maximum pod count <cce_10_0348__section16296174054019>`, increase the :ref:`maximum pod count <cce_10_0348__section16296174054019>` accordingly. Best practice: Keep the maximum pod count equal to the number of network interfaces. |                                                                                                                                                                           |
+---------------------------------------------------------------+---------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

FAQs
----

-  **How do I obtain the maximum pod count in a CCE Turbo cluster?**

   The maximum pod count per node in a CCE Turbo cluster depends on the number of network interfaces, which varies by node flavor. You can view the supported maximum pod count for each flavor when creating a node.

-  **Why does pod capacity vary between nodes with the same CPU and memory flavor?**

   Pod capacity is not determined solely by CPU and memory flavors. It also depends on the cluster type and other configuration parameters. For details, see :ref:`Calculation of the Maximum Number of Pods on a Node <cce_10_0348__section103406569524>`.
