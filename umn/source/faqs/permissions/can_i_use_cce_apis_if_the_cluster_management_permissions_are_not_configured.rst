:original_name: cce_faq_00399.html

.. _cce_faq_00399:

Can I Use CCE APIs If the Cluster Management Permissions Are Not Configured?
============================================================================

CCE has cloud service APIs and cluster APIs.

-  Cloud service APIs: You can perform operations on the infrastructure (such as creating nodes) and cluster resources (such as creating workloads).

   When using cloud service APIs, the cluster management (IAM) permissions must be configured.

-  Cluster APIs: You can perform operations on cluster resources (such as creating workloads) through the Kubernetes native API server, but not on cloud infrastructure resources (such as creating nodes).

   When using cluster APIs, you only need to add the cluster certificate. Only the users with the cluster management (IAM) permissions can download the cluster certificate. Note that information leakage may occur during certificate transmission.
