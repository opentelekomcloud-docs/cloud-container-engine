:original_name: cce_10_0367.html

.. _cce_10_0367:

Accessing a Cluster Using a Custom Domain Name
==============================================

Scenario
--------

Subject Alternative Name (SAN) allows multiple values (including IP addresses, domain names, and so on) to be associated with certificates. A SAN is usually used by the client to verify the server validity in TLS handshakes. Specifically, the validity check includes whether the server certificate is issued by a CA trusted by the client and whether the SAN in the certificate matches the IP address or DNS domain name that the client actually accesses.

If the client cannot directly access the private IP or EIP of the cluster, you can sign the IP address or DNS domain name that can be directly accessed by the client into the cluster server certificate as a SAN to enable two-way authentication on the client, which improves security. Typical use cases include DNAT access and domain name access.

If you have particular proxy access requirements or need to access resources in other regions, you can customize a SAN. Typical domain name access scenarios:

-  Add the response domain name mapping when specifying the DNS domain name address in the host domain name configuration on the client, or configuring **/etc/hosts** on the client host.
-  Use domain name access in the intranet. DNS allows you to configure mappings between cluster EIPs and custom domain names. After an EIP is updated, you can continue to use two-way authentication and the domain name to access the cluster without downloading the **kubeconfig.json** file again.
-  Add A records on a self-built DNS server.

Prerequisites
-------------

A cluster of v1.19 or later is available.

Customizing a SAN
-----------------

#. Log in to the CCE console.
#. Click the name of the target cluster in the cluster list to go to the cluster **Overview** page.
#. In the **Connection Information** area, click |image1| next to **Custom SAN**. In the dialog box displayed, enter the IP address or domain name and click **Save**.

   .. note::

      1. This operation will restart kube-apiserver and update the **kubeconfig.json** file for a short period of time. Do not perform operations on the cluster during this period.

      2. A maximum of 128 domain names or IP addresses, separated by commas (,), are allowed.

      3. If a custom domain name needs to be bound to an EIP, ensure that an EIP has been configured.

Connecting to a Cluster Using the SAN
-------------------------------------

**Using kubectl to access the cluster**

#. Download the **kubeconfig.json** file again after the SAN is modified.

   a. Log in to the CCE console and click the cluster name to access the cluster console.

   b. .. _cce_10_0367__li2323134272411:

      On the **Overview** page, locate the **Connection Info** area, click **Configure** next to **kubectl**. On the page displayed, download the configuration file.

#. Configure kubectl.

   a. Log in to your client and copy the **kubeconfig.json** file downloaded in :ref:`1.b <cce_10_0367__li2323134272411>` to the **/home** directory on your client.

   b. Configure the kubectl authentication file.

      .. code-block::

         cd /home
         mkdir -p $HOME/.kube
         mv -f kubeconfig.json $HOME/.kube/config

   c. Change the kubectl access mode and use the SAN to access the cluster.

      .. code-block::

         kubectl config use-context customSAN-0

      In the preceding command, *customSAN-0* indicates the configuration name of the custom SAN. If multiple SANs are configured, the number in the configuration name of each SAN starts from **0** and increases in ascending order, for example, *customSAN-0*, *customSAN-1*, and so on.

**Using an X.509 certificate to access the cluster**

#. After the SAN is modified, download the X509 certificate again.

   a. Log in to the CCE console and click the cluster name to access the cluster console.
   b. On the **Overview** page, locate the **Connection Info** area, and click **Download** next to **X.509 certificate**.
   c. In the **Obtain Certificate** dialog box displayed, select the certificate expiration time and download the X.509 certificate of the cluster as prompted.

#. Call native Kubernetes APIs using the cluster certificate.

   For example, run the **curl** command to call an API to obtain the pod information. In the following information, *example.com:5443* indicates the custom SAN.

   .. code-block::

      curl --cacert ./ca.crt --cert ./client.crt --key ./client.key  https://example.com:5443/api/v1/namespaces/default/pods/

   For more cluster APIs, see `Kubernetes API <https://kubernetes.io/docs/reference/kubernetes-api/>`__.

.. |image1| image:: /_static/images/en-us_image_0000001981436837.png
