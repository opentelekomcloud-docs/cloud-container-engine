:original_name: cce_10_0841.html

.. _cce_10_0841:

Configuring SNI for a LoadBalancer Service
==========================================

An SNI certificate is an extended server certificate that allows the same IP address and port number to provide multiple access domain names for external systems. Different security certificates can be used based on the domain names requested by clients to ensure HTTPS communication security.

When configuring SNI, you need to add a certificate associated with a domain name. The client submits the requested domain name information when initiating an SSL handshake request. After receiving the SSL request, the load balancer searches for the certificate based on the domain name. If the certificate is found, the load balancer will return it to the client. If the certificate is not found, the load balancer will return the default server certificate.

.. note::

   After SNI is configured, if you delete the SNI configuration on the CCE console or delete the target annotation from the YAML file, the configuration on the ELB will be retained.

Prerequisites
-------------

-  A Kubernetes cluster is available and the cluster version meets the following requirements:

   -  v1.23: v1.23.13-r0 or later
   -  v1.25: v1.25.8-r0 or later
   -  v1.27: v1.27.5-r0 or later
   -  v1.28: v1.28.3-r0 or later
   -  Other clusters of later versions

-  You have created one or more SNI certificates in ELB and specified a domain name in these certificates.
-  To create a cluster using commands, ensure kubectl is used. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

Using the CCE Console
---------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Services & Ingresses**. In the upper right corner, click **Create Service**.
#. Configure Service parameters. In this example, only mandatory parameters required for using SNI are listed. For details about how to configure other parameters, see :ref:`Creating a LoadBalancer Service <cce_10_0681__section84162025538>`.

   -  **Service Name**: Specify a Service name, which can be the same as the workload name.
   -  **Service Type**: Select **LoadBalancer**.
   -  **Selector**: Add a label and click **Confirm**. The Service will use this label to select pods. You can also click **Reference Workload Label** to use the label of an existing workload. In the dialog box that is displayed, select a workload and click **OK**.
   -  **Load Balancer**: Select a load balancer type and creation mode.

      -  .. _cce_10_0841__li1995101610373:

         A load balancer can be dedicated or shared. To enable HTTP/HTTPS on the listener port of a dedicated load balancer, the type of the load balancer must be **Application (HTTP/HTTPS)** or **Network (TCP/UDP) & Application (HTTP/HTTPS)**.

      -  This section uses an existing load balancer as an example. For details about the parameters for automatically creating a load balancer, see :ref:`Table 1 <cce_10_0681__table026610571395>`.

   -  **Ports**

      -  **Protocol**: Select **TCP**. If you select **UDP**, HTTP and HTTPS will be unavailable.

      -  **Service Port**: port used by the Service. The port number ranges from 1 to 65535.

      -  **Container Port**: listener port of the workload. For example, Nginx uses port 80 by default.

      -  .. _cce_10_0841__li8911126175719:

         **Frontend Protocol**: In this example, HTTPS must be enabled for the Service to use SNI. For a :ref:`dedicated load balancer <cce_10_0841__li1995101610373>`, to use HTTP/HTTPS, the type of the load balancer must be **Application (HTTP/HTTPS)**.

   -  **Listener**

      -  **SSL Authentication**: Select this option if :ref:`HTTPS <cce_10_0841__li8911126175719>` is enabled on the listener port. This parameter is available only in clusters of v1.23.14-r0, v1.25.9-r0, v1.27.6-r0, v1.28.4-r0, or later versions.

         -  **One-way authentication**: Only the backend server is authenticated. If you also need to authenticate the identity of the client, select mutual authentication.
         -  **Mutual authentication**: If you want the clients and the load balancer to authenticate each other, select this option. Only authenticated clients will be allowed to access the load balancer.

      -  **CA Certificate**: If **SSL Authentication** is set to **Mutual authentication**, add a CA certificate to authenticate the client. A CA certificate is issued by a certificate authority (CA) and used to verify the certificate issuer. If HTTPS mutual authentication is required, HTTPS connections can be established only when the client provides a certificate issued by a specific CA.

      -  **Server Certificate**: Select a server certificate as the default certificate.

      -  **SNI**: Add an SNI certificate containing a domain name.

         If an SNI certificate cannot be found based on the domain name requested by the client, the server certificate will be returned by default.

      -  **Security Policy**: If :ref:`HTTPS <cce_10_0841__li8911126175719>` is enabled on the listener port, you can select a security policy. This parameter is available only in clusters of v1.23.14-r0, v1.25.9-r0, v1.27.6-r0, v1.28.4-r0, or later versions.

      -  **Backend Protocol**: If :ref:`HTTPS <cce_10_0841__li8911126175719>` is enabled on the listener port, HTTP or HTTPS can be used to access the backend server. The default value is **HTTP**. This parameter is available only in clusters of v1.23.14-r0, v1.25.9-r0, v1.27.6-r0, v1.28.4-r0, or later versions.

#. Click **OK**.

Using kubectl
-------------

This section uses an existing load balancer as an example. An example YAML file of a SNI-compliant Service is as follows:

.. code-block::

   apiVersion: v1
   kind: Service
   metadata:
     name: test
     labels:
       app: test
     namespace: default
     annotations:
       kubernetes.io/elb.class: performance        # Load balancer type
       kubernetes.io/elb.id: 65318265-4f01-4541-a654-fa74e439dfd3  # ID of an existing load balancer
       kubernetes.io/elb.protocol-port: https:80    # Port where SNI is to be enabled
       kubernetes.io/elb.cert-id: b64ab636f1614e1a960b5249c497a880    # HTTPS server certificate
       kubernetes.io/elb.tls-certificate-ids: 5196aa70b0f143189e4cb54991ba2286,8125d71fcc124aabbe007610cba42d60    # SNI certificate IDs
       kubernetes.io/elb.lb-algorithm: ROUND_ROBIN
   spec:
     selector:
       app: test
     externalTrafficPolicy: Cluster
     ports:
       - name: cce-service-0
         targetPort: 80
         nodePort: 0
         port: 80
         protocol: TCP
     type: LoadBalancer
     loadBalancerIP: **.**.**.**       #  Private IP address of the load balancer

.. table:: **Table 1** Key parameters

   +---------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                             | Type                  | Description                                                                                                                                                                                                                                             |
   +=======================================+=======================+=========================================================================================================================================================================================================================================================+
   | kubernetes.io/elb.protocol-port       | String                | If a Service is HTTP/HTTPS-compliant, configure the protocol and port number in the format of "protocol:port".                                                                                                                                          |
   |                                       |                       |                                                                                                                                                                                                                                                         |
   |                                       |                       | where,                                                                                                                                                                                                                                                  |
   |                                       |                       |                                                                                                                                                                                                                                                         |
   |                                       |                       | -  **protocol**: specifies the protocol used by the listener port. The value can be **http** or **https**.                                                                                                                                              |
   |                                       |                       | -  **ports**: Service ports specified by **spec.ports[].port**.                                                                                                                                                                                         |
   |                                       |                       |                                                                                                                                                                                                                                                         |
   |                                       |                       | For example, to use SNI, the Service protocol must be **https** and the Service port must be **80**. Therefore, the parameter value is **https:80**.                                                                                                    |
   +---------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | kubernetes.io/elb.cert-id             | String                | ID of an ELB certificate, which is used as the HTTPS server certificate.                                                                                                                                                                                |
   |                                       |                       |                                                                                                                                                                                                                                                         |
   |                                       |                       | To obtain the certificate, log in to the CCE console, choose **Service List** > **Networking** > **Elastic Load Balance**, and click **Certificates** in the navigation pane. In the load balancer list, copy the ID under the target certificate name. |
   +---------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | kubernetes.io/elb.tls-certificate-ids | String                | In ELB, the IDs of SNI certificates that must contain a domain name are separated by commas (,).                                                                                                                                                        |
   |                                       |                       |                                                                                                                                                                                                                                                         |
   |                                       |                       | If an SNI certificate cannot be found based on the domain name requested by the client, the server certificate will be returned by default.                                                                                                             |
   |                                       |                       |                                                                                                                                                                                                                                                         |
   |                                       |                       | To obtain the certificate, log in to the CCE console, choose **Service List** > **Networking** > **Elastic Load Balance**, and click **Certificates** in the navigation pane. In the load balancer list, copy the ID under the target certificate name. |
   +---------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
