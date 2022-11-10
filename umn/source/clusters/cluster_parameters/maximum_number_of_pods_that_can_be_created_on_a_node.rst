:original_name: cce_01_0348.html

.. _cce_01_0348:

Maximum Number of Pods That Can Be Created on a Node
====================================================

The maximum number of pods that can be created on a node is determined by the following parameters:

-  Number of container IP addresses that can be allocated on a node (alpha.cce/fixPoolMask): Set this parameter when creating a CCE cluster. This parameter is available only when **Network Model** is **VPC network**.
-  Maximum number of pods of a node (maxPods): Set this parameter when creating a node. It is a configuration item of kubelet.
-  Number of ENIs of a CCE Turbo cluster node: In a CCE Turbo cluster, ECS nodes use sub-ENIs and BMS nodes use ENIs. The maximum number of pods that can be created on a node depends on the number of ENIs that can be used by the node.

The maximum number of pods that can be created on a node depends on the minimum value of these parameters.

-  For a cluster using the container tunnel network model, the value depends only on :ref:`the maximum number of pods on a node <cce_01_0348__section16296174054019>`.
-  For a cluster using the VPC network model, the value depends on the minimum value between :ref:`the maximum number of pods on a node <cce_01_0348__section16296174054019>` and :ref:`the number of container IP addresses that can be allocated to a node <cce_01_0348__section10770192193714>`, that is, min(maximum number of pods on a node, number of container IP addresses that can be allocated to a node).
-  For a cluster (CCE Turbo cluster) using the Cloud Native Network 2.0 model, the value depends on :ref:`the maximum number of pods on a node <cce_01_0348__section16296174054019>` and :ref:`the number of NICs on a CCE Turbo cluster node <cce_01_0348__section491514414286>`.

Container Network vs. Host Network
----------------------------------

When creating a pod, you can select the container network or host network for the pod.

-  Container network (default): **Each pod is assigned an IP address by the cluster networking add-ons, which occupies the IP addresses of the container network**.
-  Host network: The pod uses the host network (**hostNetwork: true** needs to be configured for the pod) and occupies the host port. The pod IP address is the host IP address. The pod does not occupy the IP addresses of the container network. To use the host network, you must confirm whether the container ports conflict with the host ports. Do not use the host network unless you know exactly which host port is used by which container.

.. _cce_01_0348__section10770192193714:

Number of Container IP Addresses That Can Be Allocated on a Node
----------------------------------------------------------------

If you select **VPC network** for **Network Model** when creating a CCE cluster, you also need to set the number of container IP addresses that can be allocated to each node, as shown in the following figure.

This parameter affects the maximum number of pods that can be created on a node. Each pod occupies an IP address (when the container network is used). If the number of available IP addresses is insufficient, pods cannot be created.

|image1|

By default, a node occupies three container IP addresses (network address, gateway address, and broadcast address). Therefore, the number of container IP addresses that can be allocated to a node equals the number of selected container IP addresses minus 3. For example, in the preceding figure, **the number of container IP addresses that can be allocated to a node is 125 (128 - 3)**.

.. _cce_01_0348__section16296174054019:

Maximum Number of Pods on a Node
--------------------------------

When creating a node, you can configure the maximum number of pods that can be created on the node. This parameter is a configuration item of kubelet and determines the maximum number of pods that can be created by kubelet.

|image2|

.. _cce_01_0348__section491514414286:

Number of NICs on a CCE Turbo Cluster Node
------------------------------------------

For details about the number of NICs on a CCE Turbo cluster node, see :ref:`Cloud Native Network 2.0 <cce_01_0284>`.

.. |image1| image:: /_static/images/en-us_image_0000001195057213.png
.. |image2| image:: /_static/images/en-us_image_0000001148989534.png
