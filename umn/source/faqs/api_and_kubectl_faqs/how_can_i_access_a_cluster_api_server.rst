:original_name: cce_faq_00025.html

.. _cce_faq_00025:

How Can I Access a Cluster API Server?
======================================

You can use either of the following methods to access a cluster API server:

-  (Recommended) Through the cluster API. This access mode uses certificate authentication. It is suitable for API calls on scale thanks to its direct connection to the API Server. This is a recommended option.
-  API Gateway. This access mode uses token authentication. You need to obtain a toke using your account. This access mode applies to small-scale API calls. API gateway flow control may be triggered when APIs are called on scale.
