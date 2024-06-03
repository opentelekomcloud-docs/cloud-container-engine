:original_name: cce_faq_00090.html

.. _cce_faq_00090:

Is Management Scale of a Cluster Related to the Number of Master Nodes?
=======================================================================

Management scale indicates the maximum number of nodes that can be managed by a cluster. If you select **50 nodes**, the cluster can manage a maximum of 50 nodes.

The number of master nodes varies according to the cluster specification, but is not affected by the management scale.

After the multi-master node mode is enabled, three master nodes will be created. If one of them is faulty, the cluster can still run properly. The services will not be affected.
