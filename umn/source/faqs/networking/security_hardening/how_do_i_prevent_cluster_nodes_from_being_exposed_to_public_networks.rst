:original_name: cce_faq_00267.html

.. _cce_faq_00267:

How Do I Prevent Cluster Nodes from Being Exposed to Public Networks?
=====================================================================

**Question**

How do I prevent cluster nodes from being exposed to public networks?

**Solution**

-  If access to port 22 of a cluster node is not required, you can define a security group rule that disables access to port 22.
-  Do not bind an EIP to a cluster node unless necessary.

.. meta::
   :description: Learn how to prevent Cloud Container Engine nodes from public network exposure by disabling port 22 and avoiding unnecessary EIP binding.
   :keywords: Cloud Container Engine, T Cloud Public, cluster nodes, public network exposure, security groups
