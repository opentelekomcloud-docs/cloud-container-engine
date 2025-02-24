:original_name: cce_10_0348.html

.. _cce_10_0348:

Maximum Number of Pods That Can Be Created on a Node
====================================================

Calculation of the Maximum Number of Pods on a Node
---------------------------------------------------

The maximum number of pods that can be created on a node is calculated based on the cluster type:

+---------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Network Model                                     | Maximum Number of Pods on a Node                                                                                                                                                                                                | Recommended Configuration                                                                                                                                                                                                                                                               |
+===================================================+=================================================================================================================================================================================================================================+=========================================================================================================================================================================================================================================================================================+
| Tunnel network                                    | :ref:`Maximum number of pods on a node <cce_10_0348__section16296174054019>`                                                                                                                                                    | None                                                                                                                                                                                                                                                                                    |
+---------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| VPC network                                       | The smaller value between the :ref:`maximum number of pods on a node <cce_10_0348__section16296174054019>` and the :ref:`number of container IP addresses that can be allocated on a node <cce_10_0348__section10770192193714>` | To ensure new pods run smoothly on a node, make sure that the number of pods on the node does not exceed the number of container IP addresses that can be assigned to it. If there are not enough container IP addresses available on the node, the new pods may not function properly. |
+---------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Cloud Native Network 2.0 (for CCE Turbo clusters) | The smaller value between the :ref:`maximum number of pods on a node <cce_10_0348__section16296174054019>` and the :ref:`number of ENIs on a node in a CCE Turbo cluster <cce_10_0348__section15702175115573>`                  | To ensure new pods run smoothly on a node, make sure that the number of pods on the node does not exceed the number of ENIs on it. If there are not enough ENIs available on the node, the new pods may not function properly.                                                          |
+---------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_10_0348__section10770192193714:

Number of Allocatable Container IP Addresses on a Node
------------------------------------------------------

When creating a cluster in the VPC network model, specify the number of container IP addresses that can be allocated on each node using alpha.cce/fixPoolMask based on the rules for managing container IP addresses.

The maximum number of pods that can be created on a node is determined by the number of container IP addresses that can be allocated to it. In a :ref:`container network <cce_10_0348__li13739132619599>`, each pod needs its own IP address. If there are not enough pre-allocated container IP addresses on the node, pods cannot be created. If **hostNetwork: true** is configured in the YAML file, pods will use the :ref:`host network <cce_10_0348__li13752132911597>` instead of the allocatable container IP addresses. For details, see :ref:`Pod IP Address Allocation Differences Between the Container Network and Host Network <cce_10_0348__section12428143711548>`.

By default, a node occupies three container IP addresses (network address, gateway address, and broadcast address). Therefore, the number of container IP addresses that can be allocated to a node equals the number of selected container IP addresses minus 3.

.. _cce_10_0348__section16296174054019:

Maximum Number of Pods on a Node
--------------------------------

When creating a node, you can configure the maximum number of pods (maxPods) that can be created on the node. This parameter is a configuration item of kubelet and determines the maximum number of pods that can be created by kubelet.

.. important::

   For nodes in the default node pool (**DefaultPool**), the maximum number of pods cannot be changed after the nodes are created.

   After a node in a custom node pool is created, you can modify the **max-pods** parameter in the node pool configuration to change the maximum number of pods on the node.

:ref:`Table 1 <cce_10_0348__table1710310156526>` lists the default maximum number of pods on a node based on node specifications.

.. _cce_10_0348__table1710310156526:

.. table:: **Table 1** Default maximum number of pods on a node

   ============== =========
   Memory         Max. Pods
   ============== =========
   4 GB           20
   8 GB           40
   16 GB          60
   32 GB          80
   64 GB or above 110
   ============== =========

.. _cce_10_0348__section15702175115573:

Number of Node ENIs (Available Only in CCE Turbo Clusters)
----------------------------------------------------------

In a CCE Turbo cluster, ECS nodes use sub-ENIs. The maximum number of pods that can be created on a node depends on the number of ENIs that can be used by the node.

.. _cce_10_0348__section12428143711548:

Pod IP Address Allocation Differences Between the Container Network and Host Network
------------------------------------------------------------------------------------

When creating a pod, you can select the container network or host network for the pod.

-  .. _cce_10_0348__li13739132619599:

   Container network (default): **Each pod is assigned an IP address by the cluster networking add-ons, which occupies the IP addresses of the container network**.

-  .. _cce_10_0348__li13752132911597:

   Host network: Pods with **hostNetwork: true** configured directly use the network of the host. After the configuration, the pods use the ports on the host and their IP address is identical to that of the host, **without relying on the IP addresses of the container network**. When using a host network, avoid conflicts between the pod ports and service ports on the host. Use a host network only if a particular application requires access to a specific port on the host.
