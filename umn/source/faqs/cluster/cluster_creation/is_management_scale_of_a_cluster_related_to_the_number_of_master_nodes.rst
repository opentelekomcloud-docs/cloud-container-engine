:original_name: cce_faq_00090.html

.. _cce_faq_00090:

Is Management Scale of a Cluster Related to the Number of Master Nodes?
=======================================================================

In a CCE cluster, the management scale is not directly related to the number of master nodes. These are cluster parameters that operate in different dimensions. Here are the details:

-  Cluster management scale: indicates the maximum number of nodes that a cluster can manage. For example, if you select 50 nodes, the cluster can manage up to 50 nodes. (The actual number of nodes that can be created is also constrained by the available IP addresses in the VPC subnets. Each master node also consumes one IP address.)

   The flavors of master nodes may vary with the cluster scale. However, the number of master nodes is not affected by the management scale.

-  Number of master nodes: The number of master nodes impacts the high availability of the cluster. To enhance the cluster's DR capabilities, you can choose multiple master nodes.

   For example, you can choose three master nodes for your cluster. If one of them is faulty, the cluster can still run properly. The services will not be affected.
