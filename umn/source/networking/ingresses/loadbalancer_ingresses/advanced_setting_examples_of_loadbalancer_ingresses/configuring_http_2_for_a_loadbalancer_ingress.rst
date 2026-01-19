:original_name: cce_10_0694.html

.. _cce_10_0694:

Configuring HTTP/2 for a LoadBalancer Ingress
=============================================

Ingresses can use HTTP/2 to expose Services. Connections from the load balancer to your application use HTTP/1.x by default. If your application is capable of receiving HTTP/2 requests, enable the use of HTTP/2 by referring to :ref:`Using the CCE Console to Configure HTTP/2 <cce_10_0694__section125271123193813>` or :ref:`Using kubectl to Configure HTTP/2 <cce_10_0694__section11493111062611>`.

Prerequisites
-------------

-  A CCE standard or Turbo cluster is available, and the cluster version meets the following requirements:

   -  v1.23: v1.23.13-r0 or later
   -  v1.25: v1.25.8-r0 or later
   -  v1.27: v1.27.5-r0 or later
   -  v1.28: v1.28.3-r0 or later
   -  Other clusters of later versions

-  An available workload has been deployed in the cluster for external access. If no workload is available, deploy a workload by referring to :ref:`Creating a Deployment <cce_10_0047>`, :ref:`Creating a StatefulSet <cce_10_0048>`, or :ref:`Creating a DaemonSet <cce_10_0216>`.
-  A Service for external access has been configured for the workload. :ref:`Services Supported by LoadBalancer Ingresses <cce_10_0094__section3565202819276>` lists the Service types supported by LoadBalancer ingresses.
-  You can obtain a trusted certificate from a certificate provider.

Notes and Constraints
---------------------

-  Only an HTTPS-compliant load balancer supports HTTP/2.
-  After HTTP/2 is configured, if you delete the advanced configuration for enabling HTTP/2 on the CCE console or delete the target annotation from the YAML file, HTTP/2 will be disabled on the ELB.
-  If multiple ingresses share the same external port on a load balancer, you are advised to use the same HTTP/2 configuration for these ingresses. Otherwise, the configuration of the first created ingress will take precedence. For details, see :ref:`Configuring Multiple Ingresses in a Cluster to Use the Same Load Balancer <cce_10_0954>`.

.. _cce_10_0694__section125271123193813:

Using the CCE Console to Configure HTTP/2
-----------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Choose **Services & Ingresses** in the navigation pane, click the **Ingresses** tab, and click **Create Ingress** in the upper right corner.
#. Configure ingress parameters.

   .. note::

      This example explains only key parameters for configuring HTTP/2. You can configure other parameters as required. For details, see :ref:`Creating a LoadBalancer Ingress on the Console <cce_10_0251>`.

   .. table:: **Table 1** Key parameters

      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Parameter             | Description                                                                                                                                                                                                                                                                                                          | Example                                                      |
      +=======================+======================================================================================================================================================================================================================================================================================================================+==============================================================+
      | Name                  | Enter an ingress name.                                                                                                                                                                                                                                                                                               | ingress-test                                                 |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Load Balancer         | Select a load balancer to be associated with the ingress or automatically create a load balancer.                                                                                                                                                                                                                    | Shared                                                       |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Listener              | -  **Frontend Protocol**: Select **HTTPS**.                                                                                                                                                                                                                                                                          | -  Frontend Protocol: HTTPS                                  |
      |                       |                                                                                                                                                                                                                                                                                                                      | -  External Port: 443                                        |
      |                       | -  **External Port**: specifies the port of the load balancer listener. The default HTTPS port is 443.                                                                                                                                                                                                               | -  Certificate Source: ELB server certificate                |
      |                       |                                                                                                                                                                                                                                                                                                                      | -  Server Certificate: cert-test                             |
      |                       | -  **Certificate Source**: Select **ELB server certificate**.                                                                                                                                                                                                                                                        | -  Advanced Options: HTTP2 enabled                           |
      |                       |                                                                                                                                                                                                                                                                                                                      |                                                              |
      |                       | -  **Server Certificate**: Use a certificate created on ELB.                                                                                                                                                                                                                                                         |                                                              |
      |                       |                                                                                                                                                                                                                                                                                                                      |                                                              |
      |                       |    If no certificate is available, go to the ELB console and create one.                                                                                                                                                                                                                                             |                                                              |
      |                       |                                                                                                                                                                                                                                                                                                                      |                                                              |
      |                       | -  **Advanced Options**: Add advanced configurations, select **HTTP2**, and set its status to **Enable**.                                                                                                                                                                                                            |                                                              |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Forwarding Policy     | -  **Domain Name**: Enter an actual domain name to be accessed. If it is left blank, the ingress can be accessed through the IP address. Ensure that the domain name has been registered and licensed. Once a forwarding policy is configured with a domain name specified, you must use the domain name for access. | -  Domain Name: You do not need to configure this parameter. |
      |                       | -  **Path Matching Rule**: Select **Prefix match**, **Exact match**, or **RegEx match**.                                                                                                                                                                                                                             | -  Path Matching Rule: Prefix match                          |
      |                       | -  **Path**: Enter the path provided by a backend application for external access. The path added must be valid in the backend application, or the forwarding cannot take effect.                                                                                                                                    | -  Path: /                                                   |
      |                       | -  **Destination Service**: Select an existing Service. Only Services that meet the requirements are automatically displayed in the Service list.                                                                                                                                                                    | -  Destination Service: nginx                                |
      |                       | -  **Destination Service Port**: Select the access port of the destination Service.                                                                                                                                                                                                                                  | -  Destination Service Port: 80                              |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+

#. Click **OK**.

.. _cce_10_0694__section11493111062611:

Using kubectl to Configure HTTP/2
---------------------------------

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Create a YAML file named **ingress-test.yaml**. The file name can be customized.

   .. code-block::

      vi ingress-test.yaml

   An example YAML file of an ingress associated with an existing load balancer is as follows:

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        name: ingress-test
        annotations:
          kubernetes.io/elb.id: <your_elb_id>  # Replace it with the ID of your existing load balancer.
          kubernetes.io/elb.ip: <your_elb_ip>  # Replace it with the IP of your existing load balancer.
          kubernetes.io/elb.port: '443'
          kubernetes.io/elb.http2-enable: 'true' # Enable HTTP/2.
      spec:
        tls:
        - secretName: ingress-test-secret
        rules:
        - host: ''
          http:
            paths:
            - path: '/'
              backend:
                service:
                  name: <your_service_name>  # Replace it with the name of your target Service.
                  port:
                    number: 80             # Replace 80 with the port number of your target Service.
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
              pathType: ImplementationSpecific
        ingressClassName: cce

   .. table:: **Table 2** HTTP/2 parameters

      +--------------------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                      | Mandatory       | Type            | Description                                                                                                                                                                                                                      |
      +================================+=================+=================+==================================================================================================================================================================================================================================+
      | kubernetes.io/elb.http2-enable | No              | String          | Whether HTTP/2 is enabled. Request forwarding using HTTP/2 improves access performance between your application and the load balancer. However, the load balancer still uses HTTP/1.x to forward requests to the backend server. |
      |                                |                 |                 |                                                                                                                                                                                                                                  |
      |                                |                 |                 | Options:                                                                                                                                                                                                                         |
      |                                |                 |                 |                                                                                                                                                                                                                                  |
      |                                |                 |                 | -  **true**: enabled                                                                                                                                                                                                             |
      |                                |                 |                 | -  **false**: disabled (default value)                                                                                                                                                                                           |
      |                                |                 |                 |                                                                                                                                                                                                                                  |
      |                                |                 |                 | Note: **HTTP/2 can be enabled or disabled only when the listener uses HTTPS.** This parameter is invalid and defaults to **false** when the listener protocol is HTTP.                                                           |
      +--------------------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Create an ingress.

   .. code-block::

      kubectl create -f ingress-test.yaml

   If information similar to the following is displayed, the ingress has been created:

   .. code-block::

      ingress/ingress-test created

#. Check the created ingress.

   .. code-block::

      kubectl get ingress

   If information similar to the following is displayed, the ingress has been created:

   .. code-block::

      NAME          CLASS    HOSTS     ADDRESS          PORTS   AGE
      ingress-test  cce      *         121.**.**.**     80,443  10s
