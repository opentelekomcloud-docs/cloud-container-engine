:original_name: cce_10_0367.html

.. _cce_10_0367:

Customizing a Cluster Certificate SAN
=====================================

Scenario
--------

A **Subject Alternative Name (SAN)** can be signed in to a cluster server certificate. A SAN is usually used by the client to verify the server validity in TLS handshakes. Specifically, the validity check includes whether the server certificate is issued by a CA trusted by the client and whether the SAN in the certificate matches the IP address or DNS domain name that the client actually accesses.

If the client cannot directly access the private IP or EIP of the cluster, you can sign the IP address or DNS domain name that can be directly accessed by the client into the cluster server certificate to enable two-way authentication on the client, which improves security. Typical use cases include DNAT access and domain name access.

Notes and Constraints
---------------------

This feature is available only to clusters of v1.19 and later.

Customizing a SAN
-----------------

#. Log in to the CCE console.
#. Click the target cluster in the cluster list to go to the cluster details page.
#. In the **Connection Information** area, click |image1| next to **Custom SAN**. In the dialog box displayed, add the IP address or domain name and click **Save**.

   .. note::

      1. This operation will restart kube-apiserver and update the **kubeconfig.json** file for a short period of time. Do not perform operations on the cluster during this period.

      2. A maximum of 128 domain names or IP addresses, separated by commas (,), are allowed.

      3. If a custom domain name needs to be bound to an EIP, ensure that an EIP has been configured.

Typical Domain Name Access Scenarios
------------------------------------

-  Add the response domain name mapping when specifying the DNS domain name address in the host domain name configuration on the client, or configuring **/etc/hosts** on the client host.
-  Use domain name access in the intranet. DNS allows you to configure mappings between cluster EIPs and custom domain names. After an EIP is updated, you can continue to use two-way authentication and the domain name to access the cluster without downloading the **kubeconfig.json** file again.
-  Add A records on a self-built DNS server.

.. |image1| image:: /_static/images/en-us_image_0000001517743644.png
