:original_name: cce_10_0683.html

.. _cce_10_0683:

Configuring HTTP/HTTPS for LoadBalancer Services
================================================

By default, a Layer 4 TCP/UDP listener is created for a LoadBalancer Service. You can also configure HTTP/HTTPS listeners for more refined and diverse task scheduling and to ensure secure access to applications from external networks.

Notes and Constraints
---------------------

-  Clusters must meet version requirements to expose HTTP/HTTPS Services.

   .. _cce_10_0683__table13284135311563:

   .. table:: **Table 1** Scenarios where a load balancer supports HTTP or HTTPS

      +--------------------+------------------------------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Load Balancer Type | Application                                    | Whether to Support HTTP or HTTPS | Description                                                                                                                                                                                    |
      +====================+================================================+==================================+================================================================================================================================================================================================+
      | Shared             | Interconnecting with an existing load balancer | Supported                        | None                                                                                                                                                                                           |
      +--------------------+------------------------------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      |                    | Automatically creating a load balancer         | Supported                        | None                                                                                                                                                                                           |
      +--------------------+------------------------------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Dedicated          | Interconnecting with an existing load balancer | Supported                        | Clusters must be of v1.19.16 or later. Requirements on load balancer specifications vary depending on the following minor versions:                                                            |
      |                    |                                                |                                  |                                                                                                                                                                                                |
      |                    |                                                |                                  | -  The load balancer must support both network and application load balancing for clusters of a version earlier than v1.19.16-r50, v1.21.11-r10, v1.23.9-r10, v1.27.1-r10, or v1.25.4-r10.     |
      |                    |                                                |                                  | -  The load balancer must support application load balancing for clusters v1.19.16-r50, v1.21.11-r10, v1.23.9-r10, v1.25.4-r10, v1.27.1-r10, or later.                                         |
      +--------------------+------------------------------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      |                    | Automatically creating a load balancer         | Supported                        | Clusters must be of v1.19.16 or later. Requirements on load balancer specifications vary depending on the following minor versions:                                                            |
      |                    |                                                |                                  |                                                                                                                                                                                                |
      |                    |                                                |                                  | -  The load balancer must support **both network and application load balancing** for clusters of a version earlier than v1.19.16-r50, v1.21.11-r10, v1.23.9-r10, v1.27.1-r10, or v1.25.4-r10. |
      |                    |                                                |                                  | -  The load balancer must support **application load balancing** for clusters v1.19.16-r50, v1.21.11-r10, v1.23.9-r10, v1.25.4-r10, v1.27.1-r10, or later.                                     |
      +--------------------+------------------------------------------------+----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

-  Do not connect an ingress and a Service that uses HTTP/HTTPS to the same listener of the same load balancer. Otherwise, a port conflict occurs.

Using the CCE Console
---------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Services and Ingresses**. In the upper right corner, click **Create Service**.
#. Configure Service parameters. In this example, only mandatory parameters required for using HTTP/HTTPS are listed. For details about how to configure other parameters, see :ref:`Using the CCE Console <cce_10_0681__section84162025538>`.

   -  **Service Name**: Specify a Service name, which can be the same as the workload name.
   -  **Service Type**: Select **LoadBalancer**.
   -  **Selector**: Add a label and click **Confirm**. The Service will use this label to select pods. You can also click **Reference Workload Label** to use the label of an existing workload. In the dialog box that is displayed, select a workload and click **OK**.
   -  **Load Balancer**: Select a load balancer type and creation mode.

      -  .. _cce_10_0683__li1995101610373:

         A load balancer can be dedicated or shared. To enable HTTP/HTTPS on the listener port of a dedicated load balancer, the type of the load balancer must be **Application (HTTP/HTTPS)** or **Network (TCP/UDP) & Application (HTTP/HTTPS)**.

      -  This section uses an existing load balancer as an example. For details about the parameters for automatically creating a load balancer, see :ref:`Table 1 <cce_10_0681__table026610571395>`.

   -  **Port**

      -  **Protocol**: Select **TCP**. If you select **UDP**, HTTP and HTTPS will be unavailable.

      -  **Service Port**: the port used by the Service. The port ranges from 1 to 65535.

      -  **Container Port**: the port that the workload listens on. For example, Nginx uses port 80 by default.

      -  .. _cce_10_0683__li8911126175719:

         **Frontend Protocol**: specifies whether to enable HTTP/HTTPS on the listener port. For a :ref:`dedicated load balancer <cce_10_0683__li1995101610373>`, to use HTTP/HTTPS, the type of the load balancer must be **Application (HTTP/HTTPS)**.

   -  **Listener**

      -  **SSL Authentication**: Select this option if :ref:`HTTPS <cce_10_0683__li8911126175719>` is enabled on the listener port. Dedicated load balancers are available in clusters v1.23.14-r0, v1.25.9-r0, v1.27.6-r0, v1.28.4-r0, or later.

         -  **One-way authentication**: Only the backend server is authenticated. If you also need to authenticate the identity of the client, select two-way authentication.
         -  **Two-way authentication**: Both the clients and the load balancer authenticate each other. This ensures only authenticated clients can access the load balancer. No additional backend server configuration is required if you select this option.

      -  **CA Certificate**: If **SSL Authentication** is set to **Two-way authentication**, add a CA certificate to authenticate the client. A CA certificate is issued by a Certificate Authority (CA) and is used to verify the issuer of the client's certificate. If HTTPS two-way authentication is enabled, HTTPS connections can be established only if the client provides a certificate issued by a specific CA.
      -  **Server Certificate**: required when **Frontend Protocol** is set to **HTTPS**.
      -  **SNI Certificate**: optional when **Frontend Protocol** is **HTTPS**. Before adding an SNI certificate, ensure the certificate contains a domain name.
      -  **Security Policy**: If **Frontend Protocol** is set to **HTTPS**, you can select a security policy. This parameter is only available in clusters v1.23.14-r0, v1.25.9-r0, v1.27.6-r0, v1.28.4-r0, or later.
      -  **Backend Protocol**: If **Frontend Protocol** is set to **HTTPS**, HTTP or HTTPS can be used to access the backend server. The default value is **HTTP**. This parameter is only available in clusters v1.23.14-r0, v1.25.9-r0, v1.27.6-r0, v1.28.4-r0, or later.

      .. note::

         If multiple HTTPS Services are released, all listeners will use the same certificate configuration.

#. Click **OK**.

Using kubectl
-------------

If a Service uses the HTTP or HTTPS protocol, it is important to take note of the following configuration requirements:

-  The requirements vary depending on the ELB types and cluster versions. For details, see :ref:`Table 1 <cce_10_0683__table13284135311563>`.
-  The two ports in **spec.ports** must correspond to those in **kubernetes.io/elb.protocol-port**. In this example, ports 443 and 80 are enabled with HTTPS and HTTP, respectively.

The following uses an automatically created dedicated load balancer as an example.

.. code-block::

   apiVersion: v1
   kind: Service
   metadata:
     annotations:
       # Specify the Layer 4 and Layer 7 flavors in the parameters for automatically creating a load balancer.
       kubernetes.io/elb.autocreate: '
         {
             "type": "public",
             "bandwidth_name": "cce-bandwidth-1634816602057",
             "bandwidth_chargemode": "traffic",
             "bandwidth_size": 5,
             "bandwidth_sharetype": "PER",
             "eip_type": "5_bgp",
             "available_zone": [
                 ""
             ],
             "l7_flavor_name": "L7_flavor.elb.s2.small",
             "l4_flavor_name": "L4_flavor.elb.s1.medium"
         }'
       kubernetes.io/elb.class: performance  # A dedicated load balancer
       kubernetes.io/elb.protocol-port: "https:443,http:80"  # HTTP/HTTPS and port number, which must be the same as the port numbers in spec.ports
       kubernetes.io/elb.cert-id: "17e3b4f4bc40471c86741dc3aa211379"  # The certificate ID of the LoadBalancer Service
       kubernetes.io/elb.security-pool-protocol: 'off'      # Backend security protocol. If it is enabled, the backend protocol is HTTPS. If it is not enabled, the backend protocol is HTTP.
     labels:
       app: nginx
       name: test
     name: test
     namespace: default
   spec:
     ports:
     - name: cce-service-0
       port: 443
       protocol: TCP
       targetPort: 80
     - name: cce-service-1
       port: 80
       protocol: TCP
       targetPort: 80
     selector:
       app: nginx
       version: v1
     sessionAffinity: None
     type: LoadBalancer

.. table:: **Table 2** Key parameters

   +------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                                | Type                  | Description                                                                                                                                                                                                                                                                                                                 |
   +==========================================+=======================+=============================================================================================================================================================================================================================================================================================================================+
   | kubernetes.io/elb.protocol-port          | String                | For HTTP, TLS, or HTTPS Services, specify the protocol and port in the format "protocol:port".                                                                                                                                                                                                                              |
   |                                          |                       |                                                                                                                                                                                                                                                                                                                             |
   |                                          |                       | where,                                                                                                                                                                                                                                                                                                                      |
   |                                          |                       |                                                                                                                                                                                                                                                                                                                             |
   |                                          |                       | -  **protocol**: the protocol used by the listener port. The value can be **tls**, **http** or **https**.                                                                                                                                                                                                                   |
   |                                          |                       | -  **port**: Service port specified by **spec.ports[].port**.                                                                                                                                                                                                                                                               |
   |                                          |                       |                                                                                                                                                                                                                                                                                                                             |
   |                                          |                       | In this example, ports 443 and 80 are enabled with HTTPS and HTTP, respectively. Therefore, the parameter value is *https:443,http:80*.                                                                                                                                                                                     |
   +------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | kubernetes.io/elb.cert-id                | String                | The ELB certificate ID used for TLS/HTTPS termination.                                                                                                                                                                                                                                                                      |
   |                                          |                       |                                                                                                                                                                                                                                                                                                                             |
   |                                          |                       | How to obtain: Log in to the ELB console and choose **Certificates**. In the certificate list, copy the ID under the target certificate name.                                                                                                                                                                               |
   +------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | kubernetes.io/elb.client-ca-cert-id      | String                | Required only for mutual authentication. The ELB certificate ID serves as the CA certificate.                                                                                                                                                                                                                               |
   |                                          |                       |                                                                                                                                                                                                                                                                                                                             |
   |                                          |                       | How to obtain: Log in to the ELB console and choose **Certificates**. In the certificate list, copy the ID under the target certificate name.                                                                                                                                                                               |
   |                                          |                       |                                                                                                                                                                                                                                                                                                                             |
   |                                          |                       | Dedicated load balancers are available in clusters v1.23.14-r0, v1.25.9-r0, v1.27.6-r0, v1.28.4-r0, or later.                                                                                                                                                                                                               |
   +------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | kubernetes.io/elb.security-pool-protocol | String                | If the listener frontend protocol is HTTPS, you can enable the backend security protocol. Once enabled, the backend protocol becomes HTTPS. The backend security protocol of an existing listener cannot be changed. The modification takes effect only on new listeners that are created by changing the protocol or port. |
   |                                          |                       |                                                                                                                                                                                                                                                                                                                             |
   |                                          |                       | -  **on/true**: enabled                                                                                                                                                                                                                                                                                                     |
   |                                          |                       | -  **off/false**: disabled                                                                                                                                                                                                                                                                                                  |
   |                                          |                       |                                                                                                                                                                                                                                                                                                                             |
   |                                          |                       | .. note::                                                                                                                                                                                                                                                                                                                   |
   |                                          |                       |                                                                                                                                                                                                                                                                                                                             |
   |                                          |                       |    When multiple ports are added, if the frontend protocols of some ports are not HTTPS, this configuration takes effect only for HTTPS ports.                                                                                                                                                                              |
   +------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
