:original_name: cce_10_0460.html

.. _cce_10_0460:

Kubernetes Node Taints
======================

Check Items
-----------

Check whether the taint needed for cluster upgrade exists on the node.

.. table:: **Table 1** Taint checklist

   ========================== ==========
   Taint Name                 Impact
   ========================== ==========
   node.kubernetes.io/upgrade NoSchedule
   ========================== ==========

Solution
--------

Scenario 1: The node is skipped during the cluster upgrade.

#. Configure the **kubectl** command. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Check the kubelet version of the corresponding node. The following information is expected:


   .. figure:: /_static/images/en-us_image_0000001981436653.png
      :alt: **Figure 1** kubelet version

      **Figure 1** kubelet version

   If the version of the node is different from that of other nodes, the node is skipped during the upgrade. Reset the node and upgrade the cluster again. For details about how to reset a node, see :ref:`Resetting a Node <cce_10_0003>`.

   .. note::

      Resetting a node will reset all node labels, which may affect workload scheduling. Before resetting a node, check and retain the labels that you have manually added to the node.
