:original_name: cce_10_0367.html

.. _cce_10_0367:

Accessing a Cluster Using a Custom Domain Name
==============================================

Scenario
--------

Subject Alternative Name (SAN) enables certificates to be associated with multiple values, including IP addresses and domain names. A SAN is usually used by the client to verify the server validity in TLS handshakes. Specifically, the validity check includes whether the server certificate is issued by a CA trusted by the client and whether the SAN in the certificate matches the IP address or DNS domain name that the client actually accesses.

If a client cannot access the cluster's private IP address or EIP, add the client's accessible IP address or DNS domain name to the cluster server certificate as a SAN. This enhances security through two-way authentication. Typical use cases include DNAT access and domain name access.

If you have particular proxy access requirements or need to access resources in other regions, you can customize a SAN. Typical domain name access scenarios:

-  Add the domain name mapping by either adding the DNS domain name address in the host domain name configuration on the client or configuring **/etc/hosts** on the client host.
-  Use domain name access in the intranet. DNS allows you to configure mappings between cluster EIPs and custom domain names. After an EIP is updated, you can continue to use two-way authentication and the domain name to access the cluster without downloading the **kubeconfig.json** file again.
-  Add A records on a self-built DNS server.

Prerequisites
-------------

A cluster of v1.19 or later is available.

Adding a Custom SAN
-------------------

You can add a custom SAN on the CCE console. To do so, perform the following operations:

#. Log in to the CCE console.
#. Click the name of the target cluster in the cluster list to go to the cluster **Overview** page.
#. In the **Connection Information** area, click |image1| next to **Custom SAN**. In the dialog box displayed, enter the IP addresses or domain names and click **Save**.

   .. note::

      -  The kube-apiserver will be restarted and the kubeconfig certificate will be updated, which will take approximately 5 minutes. Do not perform any operations on the cluster during this period. After the operation is complete, download the updated kubeconfig certificate.
      -  A maximum of 128 domain names or IP addresses, separated by commas (,), are allowed.
      -  If a custom domain name needs to be bound to an EIP, ensure that you have configured an EIP.

Using kubectl to Access a Cluster
---------------------------------

After the SAN is modified, the original **kubeconfig.json** file becomes invalid. If you previously accessed the cluster using kubectl, you need to reconfigure the settings.

#. .. _cce_10_0367__li8846101312517:

   Download the **kubeconfig.json** file again after the SAN is modified.

   a. Log in to the CCE console and click the cluster name to access the cluster console.
   b. On the **Overview** page, locate the **Connection Information** area, and click **Configure** next to **kubectl**. On the page displayed, download the configuration file.

#. Configure kubectl.

   a. Log in to your client and copy the **kubeconfig.json** file downloaded in :ref:`1 <cce_10_0367__li8846101312517>` to the **/home** directory on your client.

   b. Configure the kubectl authentication file.

      .. code-block::

         cd /home
         mkdir -p $HOME/.kube
         mv -f kubeconfig.json $HOME/.kube/config

   c. Change the kubectl access mode and use the SAN to access the cluster.

      .. code-block::

         kubectl config use-context customSAN-0

      In the preceding command, *customSAN-0* indicates the configuration name of the custom SAN. When multiple SANs are configured, each SAN is named with an incrementing number starting from **0**, such as *customSAN-0*, *customSAN-1*, and more.

#. Run the following command on the client to check whether the client can access the cluster using kubectl:

   .. code-block::

      kubectl cluster-info    # Check the cluster information.

   If the following information is displayed, the client can access the cluster using kubectl:

   .. code-block::

      Kubernetes control plane is running at https://xx.xx.xx.xx:5443
      CoreDNS is running at https://xx.xx.xx.xx:5443/api/v1/namespaces/kube-system/services/coredns:dns/proxy
      To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

Using an X.509 Certificate to Access a Cluster
----------------------------------------------

After the SAN is modified, the original X.509 certificate becomes invalid. If you previously accessed the cluster using the X.509 certificate, you need to reconfigure the settings.

#. After the SAN is modified, download the X.509 certificate again.

   a. Log in to the CCE console and click the cluster name to access the cluster console.
   b. On the **Overview** page, locate the **Connection Information** area, and click **Download** next to **X.509 certificate**.
   c. In the **Obtain Certificate** dialog box displayed, select the certificate validity period and download the X.509 certificate of the cluster as prompted.

#. Call Kubernetes native APIs using the cluster certificate.

   For example, run the **curl** command to call an API to obtain the pod information. In the following information, *example.com:5443* indicates the custom SAN.

   .. code-block::

      curl --cacert ./ca.crt --cert ./client.crt --key ./client.key  https://example.com:5443/api/v1/namespaces/default/pods/

   If the following information is displayed, the X.509 certificate is correctly configured and the API Server of the cluster is running properly:

   .. code-block::

      {
        "kind": "PodList",
        "apiVersion": "v1",
      ...

   For more cluster APIs, see `Kubernetes API <https://kubernetes.io/docs/reference/kubernetes-api/>`__.

.. |image1| image:: /_static/images/en-us_image_0000002483959210.png
