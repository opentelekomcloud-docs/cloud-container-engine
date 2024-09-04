:original_name: cce_10_0348.html

.. _cce_10_0348:

Maximum Number of Pods That Can Be Created on a Node
====================================================

Calculation of the Maximum Number of Pods on a Node
---------------------------------------------------

The maximum number of pods that can be created on a node is calculated based on the cluster type:

+-------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Network Model                                         | Calculation of the Maximum Number of Pods That Can Be Created on a Node                                                                                                                                                                     | Suggestions                                                                                                                                                                                                                                                                                            |
+=======================================================+=============================================================================================================================================================================================================================================+========================================================================================================================================================================================================================================================================================================+
| Container tunnel network                              | Depends on :ref:`maximum number of pods on a node <cce_10_0348__section16296174054019>`.                                                                                                                                                    | None                                                                                                                                                                                                                                                                                                   |
+-------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| **VPC network**                                       | Depends on the smaller value between the :ref:`maximum number of pods on a node <cce_10_0348__section16296174054019>` and the :ref:`number of container IP addresses that can be allocated on a node <cce_10_0348__section10770192193714>`. | To ensure proper functioning of new pods on a node, it is recommended that you limit maximum number of pods on the node to the number of container IP addresses that can be allocated on the node. If the number of container IP addresses on the node is insufficient, new pods may not run properly. |
+-------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| **Cloud Native 2.0 network** (for CCE Turbo clusters) | Depends on the smaller value between :ref:`maximum number of pods on a node <cce_10_0348__section16296174054019>` and the :ref:`number of ENIs on a node in a CCE Turbo cluster <cce_10_0348__section15702175115573>`.                      | To ensure proper functioning of new pods on a node, it is recommended that you limit the maximum number of pods on the node to the number of ENIs available on it. If the available ENIs on the node are insufficient, new pods may not run properly.                                                  |
+-------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_10_0348__section10770192193714:

Number of Container IP Addresses That Can Be Allocated on a Node
----------------------------------------------------------------

When creating a cluster using a **VPC network**, you need to configure the number of container IP addresses that can be allocated on each node (that is, the **alpha.cce/fixPoolMask** parameter) based on the allocation rules. For details about the VPC network's IP address allocation rules, see :ref:`Container IP Address Management <cce_10_0283__section1574982552114>`.

This parameter affects the maximum number of pods that can be created on a node. When :ref:`container network <cce_10_0348__li13739132619599>` is used, an IP address is allocated to every pod. If the container IP addresses pre-allocated on the node are insufficient, pods cannot be created. If pods use the :ref:`host network <cce_10_0348__li13752132911597>` (:ref:`hostNetwork: true <cce_10_0402>` configured in the YAML file), the pods do not occupy the allocatable container IP addresses. For details, see :ref:`Pod IP Address Allocation Differences Between the Container Network and Host Network <cce_10_0348__section12428143711548>`.

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

Number of Node ENIs (CCE Turbo Clusters)
----------------------------------------

In a CCE Turbo cluster, ECS nodes use sub-ENIs. The maximum number of pods that can be created on a node depends on the number of ENIs that can be used by the node.

.. _cce_10_0348__section12428143711548:

Pod IP Address Allocation Differences Between the Container Network and Host Network
------------------------------------------------------------------------------------

When creating a pod, you can select the container network or host network for the pod.

-  .. _cce_10_0348__li13739132619599:

   Container network (default): **Each pod is assigned an IP address by the cluster networking add-ons, which occupies the IP addresses of the container network**.

-  .. _cce_10_0348__li13752132911597:

   Host network: The pod uses the host network (:ref:`hostNetwork: true <cce_10_0402>` needs to be configured for the pod) and occupies the host port. The pod IP address is the host IP address. The pod does not occupy the IP addresses of the container network. To use the host network, you must confirm whether the container ports conflict with the host ports. Do not use the host network unless a specific application must use a specific port on the host.
