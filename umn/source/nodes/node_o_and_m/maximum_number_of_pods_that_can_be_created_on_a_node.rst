:original_name: cce_10_0348.html

.. _cce_10_0348:

Maximum Number of Pods That Can Be Created on a Node
====================================================

Calculation of the Maximum Number of Pods on a Node
---------------------------------------------------

The maximum number of pods that can be created on a node is calculated based on the cluster type:

-  For a cluster using the container tunnel network model, the value depends only on :ref:`the maximum number of pods on a node <cce_10_0348__section16296174054019>`.
-  For clusters using the VPC network model, the value depends on :ref:`the maximum number of pods on a node <cce_10_0348__section16296174054019>` and :ref:`the minimum number of container IP addresses that can be allocated to a node <cce_10_0348__section10770192193714>`. It is recommended that the maximum number of pods on a node be less than or equal to the number of container IP addresses that can be allocated to the node. Otherwise, pods may fail to be scheduled.
-  For CCE Turbo clusters using the Cloud Native Network 2.0 model, the value depends on :ref:`the maximum number of pods on a node <cce_10_0348__section16296174054019>` and :ref:`the minimum number of ENIs on a CCE Turbo cluster node <cce_10_0348__section15702175115573>`. It is recommended that the maximum number of pods on a node be less than or equal to the number of ENIs on the node. Otherwise, pods may fail to be scheduled.

.. _cce_10_0348__section10770192193714:

Number of Container IP Addresses That Can Be Allocated on a Node
----------------------------------------------------------------

If you select **VPC network** for **Network Model** when creating a CCE cluster, you also need to set the number of container IP addresses that can be allocated to each node (alpha.cce/fixPoolMask). If the pod uses the host network (**hostNetwork: true**), the pod does not occupy the IP address of the allocatable container network. For details, see :ref:`Container Network vs. Host Network <cce_10_0348__section12428143711548>`.

This parameter affects the maximum number of pods that can be created on a node. Each pod occupies an IP address (when the :ref:`container network <cce_10_0348__li13739132619599>` is used). If the number of available IP addresses is insufficient, pods cannot be created. If the pod uses the host network (**hostNetwork: true**), the pod does not occupy the IP address of the allocatable container network.

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

   =============== =========
   Memory          Max. Pods
   =============== =========
   4 GiB           20
   8 GiB           40
   16 GiB          60
   32 GiB          80
   64 GiB or above 110
   =============== =========

.. _cce_10_0348__section15702175115573:

Number of Node ENIs (CCE Turbo Clusters)
----------------------------------------

In a CCE Turbo cluster, ECSs use sub-ENIs. The maximum number of pods that can be created on a node depends on the number of ENIs that can be used by the node.

.. _cce_10_0348__section12428143711548:

Container Network vs. Host Network
----------------------------------

When creating a pod, you can select the container network or host network for the pod.

-  .. _cce_10_0348__li13739132619599:

   Container network (default): **Each pod is assigned an IP address by the cluster networking add-ons, which occupies the IP addresses of the container network**.

-  Host network: The pod uses the host network (**hostNetwork: true** needs to be configured for the pod) and occupies the host port. The pod IP address is the host IP address. The pod does not occupy the IP addresses of the container network. To use the host network, you must confirm whether the container ports conflict with the host ports. Do not use the host network unless you know exactly which host port is used by which container.
