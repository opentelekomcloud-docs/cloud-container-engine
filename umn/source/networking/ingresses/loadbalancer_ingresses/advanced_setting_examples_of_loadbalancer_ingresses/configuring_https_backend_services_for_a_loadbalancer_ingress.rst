:original_name: cce_10_0691.html

.. _cce_10_0691:

Configuring HTTPS Backend Services for a LoadBalancer Ingress
=============================================================

Ingresses can interconnect with backend services of different protocols. By default, the backend proxy channel of an ingress is HTTP-compliant. To create an HTTPS channel, add the following configuration to the **annotations** field:

.. code-block:: text

   kubernetes.io/elb.pool-protocol: https

Prerequisites
-------------

-  A CCE standard or Turbo cluster is available, and the cluster version meets the following requirements:

   -  v1.23: v1.23.8-r0 or later
   -  v1.25: v1.25.3-r0 or later
   -  Other clusters of later versions

-  An available workload has been deployed in the cluster for external access. If no workload is available, deploy a workload by referring to :ref:`Creating a Deployment <cce_10_0047>`, :ref:`Creating a StatefulSet <cce_10_0048>`, or :ref:`Creating a DaemonSet <cce_10_0216>`.
-  A Service for external access has been configured for the workload. :ref:`Services Supported by LoadBalancer Ingresses <cce_10_0094__section3565202819276>` lists the Service types supported by LoadBalancer ingresses.
-  You can obtain a trusted certificate from a certificate provider.

Notes and Constraints
---------------------

-  Ingresses can interconnect with HTTPS backend services only when dedicated load balancers are used.
-  When an ingress interconnects with an HTTPS backend service, the ingress protocol must be HTTPS.
-  Load balancers do not support updating backend service protocols. Modifications are not allowed after an ingress is created.

Using the CCE Console to Configure an HTTPS Backend Service
-----------------------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Choose **Services & Ingresses** in the navigation pane, click the **Ingresses** tab, and click **Create Ingress** in the upper right corner.
#. Configure ingress parameters.

   .. note::

      This example explains only key parameters for configuring HTTPS backend Services. You can configure other parameters as required. For details, see :ref:`Creating a LoadBalancer Ingress on the Console <cce_10_0251>`.

   .. table:: **Table 1** Key parameters

      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Parameter             | Description                                                                                                                                                                                                                                                                                                          | Example                                                      |
      +=======================+======================================================================================================================================================================================================================================================================================================================+==============================================================+
      | Name                  | Enter an ingress name.                                                                                                                                                                                                                                                                                               | ingress-test                                                 |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Load Balancer         | Select a load balancer to be associated with the ingress or automatically create a load balancer. In this example, only dedicated load balancers are supported.                                                                                                                                                      | Dedicated                                                    |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Listener              | -  **Frontend Protocol**: Select **HTTPS** for an HTTPS backend Service of an ingress.                                                                                                                                                                                                                               | -  Frontend Protocol: HTTPS                                  |
      |                       |                                                                                                                                                                                                                                                                                                                      | -  External Port: 443                                        |
      |                       | -  **External Port**: specifies the port of the load balancer listener. The default HTTPS port is 443.                                                                                                                                                                                                               | -  Certificate Source: ELB server certificate                |
      |                       |                                                                                                                                                                                                                                                                                                                      | -  Server Certificate: cert-test                             |
      |                       | -  **Certificate Source**: Select **ELB server certificate**.                                                                                                                                                                                                                                                        | -  Backend Protocol: HTTPS                                   |
      |                       |                                                                                                                                                                                                                                                                                                                      |                                                              |
      |                       | -  **Server Certificate**: Use a certificate created on ELB.                                                                                                                                                                                                                                                         |                                                              |
      |                       |                                                                                                                                                                                                                                                                                                                      |                                                              |
      |                       |    If no certificate is available, go to the ELB console and create one.                                                                                                                                                                                                                                             |                                                              |
      |                       |                                                                                                                                                                                                                                                                                                                      |                                                              |
      |                       | -  **Backend Protocol**: Select **HTTPS**.                                                                                                                                                                                                                                                                           |                                                              |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Forwarding Policy     | -  **Domain Name**: Enter an actual domain name to be accessed. If it is left blank, the ingress can be accessed through the IP address. Ensure that the domain name has been registered and licensed. Once a forwarding policy is configured with a domain name specified, you must use the domain name for access. | -  Domain Name: You do not need to configure this parameter. |
      |                       | -  **Path Matching Rule**: Select **Prefix match**, **Exact match**, or **RegEx match**.                                                                                                                                                                                                                             | -  Path Matching Rule: Prefix match                          |
      |                       | -  **Path**: Enter the path provided by a backend application for external access. The path added must be valid in the backend application, or the forwarding cannot take effect.                                                                                                                                    | -  Path: /                                                   |
      |                       | -  **Destination Service**: Select an existing Service. Only Services that meet the requirements are automatically displayed in the Service list.                                                                                                                                                                    | -  Destination Service: nginx                                |
      |                       | -  **Destination Service Port**: Select the access port of the destination Service.                                                                                                                                                                                                                                  | -  Destination Service Port: 80                              |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+

#. Click **OK**.

Using kubectl to Configure an HTTPS Backend Service
---------------------------------------------------

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
        namespace: default
        annotations:
          kubernetes.io/elb.port: '443'
          kubernetes.io/elb.id: <your_elb_id>    # In this example, an existing dedicated load balancer is used. Replace its ID with the ID of your dedicated load balancer.
          kubernetes.io/elb.class: performance
          kubernetes.io/elb.pool-protocol: https  # Interconnected HTTPS backend service
          kubernetes.io/elb.tls-ciphers-policy: tls-1-2
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
                        number: 80
                  property:
                    ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
                  pathType: ImplementationSpecific
        ingressClassName: cce

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
