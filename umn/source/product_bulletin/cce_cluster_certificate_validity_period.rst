:original_name: cce_bulletin_0136.html

.. _cce_bulletin_0136:

CCE Cluster Certificate Validity Period
=========================================

**Released**: Oktober 23, 2026

When a cluster is created, CCE automatically generates a cluster certificate to secure communication between cluster control plane components and cluster nodes. Alternatively, you can specify your own certificate during cluster creation for identity authentication proxy and extended API server authentication. Both CCE-generated and custom certificates have expiration dates. If a certificate expires, cluster functions may become abnormal, potentially affecting service operations.

You must pay close attention to the validity periods of both the cluster certificate and any custom certificate. Certificates must be updated before they expire. For details, see :ref:`Rotating Cluster Certificates <cce_10_1080>`.