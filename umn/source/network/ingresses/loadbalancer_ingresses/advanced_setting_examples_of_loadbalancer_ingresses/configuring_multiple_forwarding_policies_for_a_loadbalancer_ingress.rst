:original_name: cce_10_0689.html

.. _cce_10_0689:

Configuring Multiple Forwarding Policies for a LoadBalancer Ingress
===================================================================

An ingress can route requests to multiple backend Services based on different matching policies. For example, requests can be routed to three different backend Services separately by accessing **www.example.com/foo**, **www.example.com/bar**, and **foo.example.com/**.

.. important::

   The URL registered in an ingress forwarding policy must be the same as the URL used to access the backend Service. Otherwise, a 404 error will be returned.

   For example, the default access URL of the Nginx application is **/usr/share/nginx/html**. When adding **/test** to the ingress forwarding policy, ensure the access URL of your Nginx application contains **/usr/share/nginx/html/test**. Otherwise, error 404 will be returned.

Prerequisites
-------------

-  An available workload has been deployed in the cluster for external access. If no workload is available, deploy a workload by referring to :ref:`Creating a Deployment <cce_10_0047>`, :ref:`Creating a StatefulSet <cce_10_0048>`, or :ref:`Creating a DaemonSet <cce_10_0216>`.
-  A Service for external access has been configured for the workload. :ref:`Services Supported by LoadBalancer Ingresses <cce_10_0094__section3565202819276>` lists the Service types supported by LoadBalancer ingresses.

Using the CCE Console
---------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Choose **Services & Ingresses** in the navigation pane, click the **Ingresses** tab, and click **Create Ingress** in the upper right corner.
#. Configure ingress parameters.

   .. note::

      This example explains only key parameters for configuring forwarding policies. You can configure other parameters as required. For details, see :ref:`Creating a LoadBalancer Ingress on the Console <cce_10_0251>`.

   .. table:: **Table 1** Key parameters

      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------+
      | Parameter             | Description                                                                                                                                                                                                                                                                                                          | Example                             |
      +=======================+======================================================================================================================================================================================================================================================================================================================+=====================================+
      | Name                  | Enter an ingress name.                                                                                                                                                                                                                                                                                               | ingress-test                        |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------+
      | Load Balancer         | Select a load balancer to be associated with the ingress or automatically create a load balancer.                                                                                                                                                                                                                    | Shared                              |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------+
      | Listener              | -  **External Protocol**: **HTTP** and **HTTPS** are available.                                                                                                                                                                                                                                                      | -  External Protocol: HTTP          |
      |                       | -  **External Port**: specifies the port of the load balancer listener.                                                                                                                                                                                                                                              | -  External Port: 80                |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------+
      | Forwarding Policy     | -  **Domain Name**: Enter an actual domain name to be accessed. If it is left blank, the ingress can be accessed through the IP address. Ensure that the domain name has been registered and licensed. Once a forwarding policy is configured with a domain name specified, you must use the domain name for access. | Forwarding rule 1:                  |
      |                       | -  **Path Matching Rule**: Select **Prefix match**, **Exact match**, or **RegEx match**.                                                                                                                                                                                                                             |                                     |
      |                       | -  **Path**: Enter the path provided by a backend application for external access. The path added must be valid in the backend application, or the forwarding cannot take effect.                                                                                                                                    | -  Domain Name: www.example.com     |
      |                       | -  **Destination Service**: Select an existing Service. Only Services that meet the requirements are automatically displayed in the Service list.                                                                                                                                                                    | -  Path Matching Rule: Prefix match |
      |                       | -  **Destination Service Port**: Select the access port of the destination Service.                                                                                                                                                                                                                                  | -  Path: /foo                       |
      |                       |                                                                                                                                                                                                                                                                                                                      | -  Destination Service: nginx       |
      |                       |                                                                                                                                                                                                                                                                                                                      | -  Destination Service Port: 80     |
      |                       |                                                                                                                                                                                                                                                                                                                      |                                     |
      |                       |                                                                                                                                                                                                                                                                                                                      | Forwarding rule 2:                  |
      |                       |                                                                                                                                                                                                                                                                                                                      |                                     |
      |                       |                                                                                                                                                                                                                                                                                                                      | -  Domain Name: www.example.com     |
      |                       |                                                                                                                                                                                                                                                                                                                      | -  Path Matching Rule: Prefix match |
      |                       |                                                                                                                                                                                                                                                                                                                      | -  Path: /bar                       |
      |                       |                                                                                                                                                                                                                                                                                                                      | -  Destination Service: nginx       |
      |                       |                                                                                                                                                                                                                                                                                                                      | -  Destination Service Port: 80     |
      |                       |                                                                                                                                                                                                                                                                                                                      |                                     |
      |                       |                                                                                                                                                                                                                                                                                                                      | Forwarding rule 3:                  |
      |                       |                                                                                                                                                                                                                                                                                                                      |                                     |
      |                       |                                                                                                                                                                                                                                                                                                                      | -  Domain Name: foo.example.com     |
      |                       |                                                                                                                                                                                                                                                                                                                      | -  Path Matching Rule: Prefix match |
      |                       |                                                                                                                                                                                                                                                                                                                      | -  Path: /                          |
      |                       |                                                                                                                                                                                                                                                                                                                      | -  Destination Service: nginx       |
      |                       |                                                                                                                                                                                                                                                                                                                      | -  Destination Service Port: 80     |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------+

#. Click **OK**.

Using kubectl
-------------

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Create a YAML file named **ingress-test.yaml**. The file name can be customized.

   .. code-block::

      vi ingress-test.yaml

   An example YAML file of an ingress associated with an existing load balancer is as follows:

   **For clusters of v1.23 or later:**

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        name: ingress-test
        annotations:
          kubernetes.io/elb.id: <your_elb_id>  #Replace it with the ID of your existing load balancer.
          kubernetes.io/elb.class: performance  # Load balancer type
          kubernetes.io/elb.port: '80'
      spec:
        rules:
        - host: 'www.example.com'
          http:
            paths:
            - path: '/foo'
              backend:
                service:
                  name: <your_service_name>  # Replace it with the name of your target Service.
                  port:
                    number: 80             # Replace 80 with the port number of your target Service.
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
              pathType: ImplementationSpecific
            - path: '/bar'
              backend:
                service:
                  name: <your_service_name>  # Replace it with the name of your target Service.
                  port:
                    number: 80             # Replace 80 with the port number of your target Service.
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
              pathType: ImplementationSpecific
        - host: 'foo.example.com'
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

   **For clusters of v1.21 or earlier:**

   .. code-block::

      apiVersion: networking.k8s.io/v1beta1
      kind: Ingress
      metadata:
        name: ingress-test
        annotations:
          kubernetes.io/ingress.class: cce
          kubernetes.io/elb.port: '80'
          kubernetes.io/elb.id: <your_elb_id>  #Replace it with the ID of your existing load balancer.
          kubernetes.io/elb.class: performance  # Load balancer type
      spec:
        rules:
        - host: 'www.example.com'
          http:
            paths:
            - path: '/foo'
              backend:
                serviceName: <your_service_name>  # Replace it with the name of your target Service.
                servicePort: 80
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
            - path: '/bar'
              backend:
                serviceName: <your_service_name>  # Replace it with the name of your target Service.
                servicePort: 80
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
        - host: 'foo.example.com'
          http:
            paths:
            - path: '/'
              backend:
                serviceName: <your_service_name>  # Replace it with the name of your target Service.
                servicePort: 80
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH

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

      NAME           CLASS  HOSTS                               ADDRESS          PORTS   AGE
      ingress-test   cce    www.example.com,foo.example.com     121.**.**.**     80      10s
