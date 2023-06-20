:original_name: cce_10_0460.html

.. _cce_10_0460:

Kubernetes Node Taint
=====================

Check Item
----------

Check whether the taint, as shown in :ref:`Table 1 <cce_10_0460__table1126154011128>`, exists on the node.

.. _cce_10_0460__table1126154011128:

.. table:: **Table 1** Taint checklist

   ========================== ==========
   Name                       Impact
   ========================== ==========
   node.kubernetes.io/upgrade NoSchedule
   ========================== ==========

Solution
--------

Scenario 1: The node is skipped during the cluster upgrade.

#. For details about how to configure kubectl, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Check the kubelet version of the corresponding node. If the following information is expected:

   |image1|

   If the version of the node is different from that of other nodes, the node is skipped during the upgrade. Reset the node and upgrade the cluster again. For details about how to reset a node, see :ref:`Resetting a Node <cce_10_0003>`.

   .. note::

      Resetting a node will reset all node labels, which may affect workload scheduling. Before resetting a node, check and retain the labels that you have manually added to the node.

.. |image1| image:: /_static/images/en-us_image_0000001568902601.png
