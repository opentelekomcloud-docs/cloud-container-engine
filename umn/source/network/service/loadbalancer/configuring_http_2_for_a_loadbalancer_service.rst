:original_name: cce_10_0842.html

.. _cce_10_0842:

Configuring HTTP/2 for a LoadBalancer Service
=============================================

Services can be exposed via HTTP/2. By default, HTTP/1.x is used between clients and load balancers. HTTP/2 can improve access performance between clients and load balancers, but HTTP/1.x is still used between load balancers and backend servers.

.. note::

   -  An HTTPS-compliant load balancer supports HTTP/2.
   -  After HTTP/2 is configured, if you delete the advanced configuration for enabling HTTP/2 on the CCE console or delete the target annotation from the YAML file, the configuration on the ELB will be retained.

Prerequisites
-------------

-  A Kubernetes cluster is available and the cluster version meets the following requirements:

   -  v1.23: v1.23.13-r0 or later
   -  v1.25: v1.25.8-r0 or later
   -  v1.27: v1.27.5-r0 or later
   -  v1.28: v1.28.3-r0 or later
   -  Other clusters of later versions

-  To create a cluster using commands, ensure kubectl is used. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

Using the CCE Console
---------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Services & Ingresses**. In the upper right corner, click **Create Service**.
#. Configure Service parameters. In this example, only mandatory parameters are listed. For details about how to configure other parameters, see :ref:`Creating a LoadBalancer Service <cce_10_0681__section84162025538>`.

   -  **Service Name**: Specify a Service name, which can be the same as the workload name.
   -  **Service Type**: Select **LoadBalancer**.
   -  **Selector**: Add a label and click **Confirm**. The Service will use this label to select pods. You can also click **Reference Workload Label** to use the label of an existing workload. In the dialog box that is displayed, select a workload and click **OK**.
   -  **Load Balancer**: Select a load balancer type and creation mode.

      -  .. _cce_10_0842__li1995101610373:

         A load balancer can be dedicated or shared. To enable HTTP/HTTPS on the listener port of a dedicated load balancer, the type of the load balancer must be **Application (HTTP/HTTPS)** or **Network (TCP/UDP) & Application (HTTP/HTTPS)**.

      -  This section uses an existing load balancer as an example. For details about the parameters for automatically creating a load balancer, see :ref:`Table 1 <cce_10_0681__table026610571395>`.

   -  **Ports**

      -  **Protocol**: Select **TCP**. If you select **UDP**, HTTP and HTTPS will be unavailable.
      -  **Service Port**: port used by the Service. The port number ranges from 1 to 65535.
      -  **Container Port**: listener port of the workload. For example, Nginx uses port 80 by default.
      -  **Frontend Protocol**: In this example, HTTPS must be enabled for the Service to use HTTP/2. For a :ref:`dedicated load balancer <cce_10_0842__li1995101610373>`, to use HTTP/HTTPS, the type of the load balancer must be **Application (HTTP/HTTPS)**.

   -  **Listener**

      -  SSL authentication is available only in clusters of v1.23.14-r0, v1.25.9-r0, v1.27.6-r0, v1.28.4-r0, or later versions.

         -  **One-way authentication**: Only the backend server is authenticated. If you also need to authenticate the identity of the client, select mutual authentication.
         -  **Mutual authentication**: If you want the clients and the load balancer to authenticate each other, select this option. Only authenticated clients will be allowed to access the load balancer.

      -  **CA Certificate**: If **SSL Authentication** is set to **Mutual authentication**, add a CA certificate to authenticate the client. A CA certificate is issued by a certificate authority (CA) and used to verify the certificate issuer. If HTTPS mutual authentication is required, HTTPS connections can be established only when the client provides a certificate issued by a specific CA.
      -  **Server Certificate**: Select a server certificate when HTTPS is used.
      -  **SNI**: Add an SNI certificate containing a domain name.
      -  **Advanced Options**: Click **Add custom container network settings** and enable HTTP/2.

#. Click **OK**.

Using kubectl
-------------

To enable HTTP/2, add the following annotation:

.. code-block::

   kubernetes.io/elb.http2-enable: 'true'

The following shows an example YAML file where an existing load balancer is associated:

.. code-block::

   apiVersion: v1
   kind: Service
   metadata:
     name: test
     labels:
       app: test
       version: v1
     namespace: default
     annotations:
       kubernetes.io/elb.class: performance
       kubernetes.io/elb.id: 35cb350b-23e6-4551-ac77-10d5298f5204
       kubernetes.io/elb.protocol-port: https:443
       kubernetes.io/elb.cert-id: b64ab636f1614e1a960b5249c497a880
       kubernetes.io/elb.http2-enable: 'true'
       kubernetes.io/elb.lb-algorithm: ROUND_ROBIN
   spec:
     selector:
       app: test
       version: v1
     externalTrafficPolicy: Cluster
     ports:
       - name: cce-service-0
         targetPort: 80
         nodePort: 0
         port: 443
         protocol: TCP
     type: LoadBalancer
     loadBalancerIP: **.**.**.**

.. table:: **Table 1** HTTP/2 parameters

   +---------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                       | Type                  | Description                                                                                                                                                                                                                                             |
   +=================================+=======================+=========================================================================================================================================================================================================================================================+
   | kubernetes.io/elb.protocol-port | String                | If a Service is HTTP/HTTPS-compliant, configure the protocol and port number in the format of "protocol:port".                                                                                                                                          |
   |                                 |                       |                                                                                                                                                                                                                                                         |
   |                                 |                       | where,                                                                                                                                                                                                                                                  |
   |                                 |                       |                                                                                                                                                                                                                                                         |
   |                                 |                       | -  **protocol**: specifies the protocol used by the listener port. The value can be **http** or **https**.                                                                                                                                              |
   |                                 |                       | -  **ports**: Service ports specified by **spec.ports[].port**.                                                                                                                                                                                         |
   |                                 |                       |                                                                                                                                                                                                                                                         |
   |                                 |                       | For example, to use HTTPS, the Service port must be **443**. Therefore, the parameter value is **https:443**.                                                                                                                                           |
   +---------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | kubernetes.io/elb.cert-id       | String                | ID of an ELB certificate, which is used as the HTTPS server certificate.                                                                                                                                                                                |
   |                                 |                       |                                                                                                                                                                                                                                                         |
   |                                 |                       | To obtain the certificate, log in to the CCE console, choose **Service List** > **Networking** > **Elastic Load Balance**, and click **Certificates** in the navigation pane. In the load balancer list, copy the ID under the target certificate name. |
   +---------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | kubernetes.io/elb.http2-enable  | String                | Whether HTTP/2 is enabled. Request forwarding using HTTP/2 improves the access performance between your application and the load balancer. However, the load balancer still uses HTTP/1.x to forward requests to the backend server.                    |
   |                                 |                       |                                                                                                                                                                                                                                                         |
   |                                 |                       | Options:                                                                                                                                                                                                                                                |
   |                                 |                       |                                                                                                                                                                                                                                                         |
   |                                 |                       | -  **true**: enabled                                                                                                                                                                                                                                    |
   |                                 |                       | -  **false**: disabled (default value)                                                                                                                                                                                                                  |
   |                                 |                       |                                                                                                                                                                                                                                                         |
   |                                 |                       | Note: **HTTP/2 can be enabled or disabled only when the listener uses HTTPS.** This parameter is invalid and defaults to **false** when the listener protocol is HTTP.                                                                                  |
   +---------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
