:original_name: cce_10_0175.html

.. _cce_10_0175:

Accessing a Cluster Using an X.509 Certificate
==============================================

X.509 certificates are essential for verifying identities and encrypting communication within CCE clusters. These certificates enable authorized clients to access target clusters while encrypting data transmission between them. This prevents threats like eavesdropping and tampering, ensuring secure communication, authenticated identities, and valid access. To initiate a connection using X.509 certificates, obtain the cluster certificate from the CCE console and use it to configure the client accordingly.

Procedure
---------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. On the **Overview** page, locate the **Connection Information** area, and click **Download** next to **X.509 certificate**.

#. In the **Obtain Certificate** dialog box displayed, select the certificate expiration time and download the X.509 certificate of the cluster as prompted.


   .. figure:: /_static/images/en-us_image_0000002434080488.png
      :alt: **Figure 1** Downloading a certificate

      **Figure 1** Downloading a certificate

   .. important::

      -  The downloaded certificate contains three files: **client.key**, **client.crt**, and **ca.crt**. Keep these files secure.
      -  Certificates are not required for mutual access between containers in a cluster.
      -  An issued X.509 certificate remains valid even if the user who requested it is deleted. To ensure cluster security, manually revoke the user's cluster access credentials. For details, see :ref:`Revoking a Cluster Access Credential <cce_10_0744>`.

#. Import the X.509 certificate to the client and use the certificate to call Kubernetes native APIs.

   For example, run the **curl** command to call an API to obtain the pod information. The following is an example:

   .. code-block::

      curl --cacert ./ca.crt --cert ./client.crt --key ./client.key  https://192.168.0.18:5443/api/v1/namespaces/default/pods/

   .. note::

      -  *./ca.crt*, *./client.crt*, and *./client.key* are the paths for uploading the **client.key**, **client.crt**, and **ca.crt** files, respectively.
      -  *192.168.0.18:5443* is the private or public network address of the API server in the cluster.

   If the following information is displayed, the X.509 certificate is correctly configured and the API Server of the cluster is running properly:

   .. code-block::

      {
        "kind": "PodList",
        "apiVersion": "v1",
      ...

   For more cluster APIs, see `Kubernetes API <https://kubernetes.io/docs/reference/kubernetes-api/>`__.
