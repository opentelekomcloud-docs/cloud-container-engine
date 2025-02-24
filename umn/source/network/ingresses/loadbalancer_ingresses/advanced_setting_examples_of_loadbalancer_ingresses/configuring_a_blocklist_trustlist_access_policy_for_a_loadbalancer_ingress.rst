:original_name: cce_10_0832.html

.. _cce_10_0832:

Configuring a Blocklist/Trustlist Access Policy for a LoadBalancer Ingress
==========================================================================

You can add IP addresses to a trustlist or blocklist to control access to a listener of a LoadBalancer ingress.

-  Trustlist: Only the IP addresses in the list can access the listener.
-  Blocklist: The IP addresses in the list are not allowed to access the listener.

.. note::

   After a blocklist/trustlist access policy is configured, if you delete the blocklist/trustlist access policy configuration on the CCE console or delete the target annotation from the YAML file, the configuration on the ELB will be retained.

Prerequisites
-------------

-  A CCE standard or Turbo cluster is available, and the cluster version meets the following requirements:

   -  v1.23: v1.23.12-r0 or later
   -  v1.25: v1.25.7-r0 or later
   -  v1.27: v1.27.4-r0 or later
   -  v1.28: v1.28.2-r0 or later
   -  Other clusters of later versions

-  An IP address group has been created on the ELB console. For details, see .

Using the CCE Console to Configure a Blocklist/Trustlist Access Policy
----------------------------------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Choose **Services & Ingresses** in the navigation pane, click the **Ingresses** tab, and click **Create Ingress** in the upper right corner.
#. Configure ingress parameters.

   .. note::

      This example explains only key parameters for configuring blocklist/trustlist access policies. You can configure other parameters as required. For details, see :ref:`Creating a LoadBalancer Ingress on the Console <cce_10_0251>`.

   .. table:: **Table 1** Key parameters

      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Parameter             | Description                                                                                                                                                                                                                                                                                                          | Example                                                      |
      +=======================+======================================================================================================================================================================================================================================================================================================================+==============================================================+
      | Name                  | Enter an ingress name.                                                                                                                                                                                                                                                                                               | ingress-test                                                 |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Load Balancer         | Select a load balancer to be associated with the ingress or automatically create a load balancer. A load balancer can be dedicated or shared.                                                                                                                                                                        | Dedicated                                                    |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Listener              | -  **External Protocol**: **HTTP** and **HTTPS** are available.                                                                                                                                                                                                                                                      | -  External Protocol: HTTP                                   |
      |                       | -  **External Port**: specifies the port of the load balancer listener.                                                                                                                                                                                                                                              |                                                              |
      |                       | -  **Access Control**                                                                                                                                                                                                                                                                                                | -  External Port: 80                                         |
      |                       |                                                                                                                                                                                                                                                                                                                      |                                                              |
      |                       |    -  **Allow all IP addresses**: No access control is configured.                                                                                                                                                                                                                                                   | -  Advanced Options                                          |
      |                       |    -  **Trustlist**: Only the selected IP address group can access the load balancer.                                                                                                                                                                                                                                |                                                              |
      |                       |    -  **Blocklist**: The selected IP address group cannot access the load balancer.                                                                                                                                                                                                                                  |    Access Control: Blocklist                                 |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+
      | Forwarding Policy     | -  **Domain Name**: Enter an actual domain name to be accessed. If it is left blank, the ingress can be accessed through the IP address. Ensure that the domain name has been registered and licensed. Once a forwarding policy is configured with a domain name specified, you must use the domain name for access. | -  Domain Name: You do not need to configure this parameter. |
      |                       | -  **Path Matching Rule**: Select **Prefix match**, **Exact match**, or **RegEx match**.                                                                                                                                                                                                                             | -  Path Matching Rule: Prefix match                          |
      |                       | -  **Path**: Enter the path provided by a backend application for external access. The path added must be valid in the backend application, or the forwarding cannot take effect.                                                                                                                                    | -  Path: /                                                   |
      |                       | -  **Destination Service**: Select an existing Service or create a Service. Any Services that do not match the search criteria will be filtered out automatically.                                                                                                                                                   | -  Destination Service: nginx                                |
      |                       | -  **Destination Service Port**: Select the access port of the destination Service.                                                                                                                                                                                                                                  | -  Destination Service Port: 80                              |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------+

#. Click **OK**.

Using kubectl to Configure a Blocklist/Trustlist Access Policy
--------------------------------------------------------------

#. Use kubectl to access the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create a YAML file named **ingress-test.yaml**. The file name can be customized.

   .. code-block::

      vi ingress-test.yaml

   An example YAML file of an ingress created using an existing load balancer is as follows:

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        name: ingress-test
        annotations:
          kubernetes.io/elb.id: <your_elb_id>                    # Load balancer ID. Replace it with the actual value.
          kubernetes.io/elb.class: performance                   # Load balancer type
          kubernetes.io/elb.port: '80'                           # External port of the load balancer listener
          kubernetes.io/elb.acl-id: <your_acl_id>               # ID of an IP address group for accessing a load balancer
          kubernetes.io/elb.acl-status: 'on'                    # Enable access control.
          kubernetes.io/elb.acl-type: 'white'                   # Trustlist for access control
      spec:
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

   .. table:: **Table 2** Annotations for ELB access control

      +------------------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                    | Type                  | Description                                                                                                                                                                                                                                                                                                |
      +==============================+=======================+============================================================================================================================================================================================================================================================================================================+
      | kubernetes.io/elb.acl-id     | String                | -  If this parameter is not specified, CCE does not modify access control on the ELB.                                                                                                                                                                                                                      |
      |                              |                       |                                                                                                                                                                                                                                                                                                            |
      |                              |                       | -  If this parameter is left empty, all IP addresses are allowed to access the load balancer.                                                                                                                                                                                                              |
      |                              |                       |                                                                                                                                                                                                                                                                                                            |
      |                              |                       | -  If this parameter is set to the IP address group ID of the load balancer, access control is enabled and you need to configure an IP address blocklist or trustlist for the load balancer. Additionally, you need to configure both **kubernetes.io/elb.acl-status** and **kubernetes.io/elb.acl-type**. |
      |                              |                       |                                                                                                                                                                                                                                                                                                            |
      |                              |                       |    **How to obtain**:                                                                                                                                                                                                                                                                                      |
      |                              |                       |                                                                                                                                                                                                                                                                                                            |
      |                              |                       |    Log in to the console. In the **Service List**, choose **Networking** > **Elastic Load Balance**. On the Network Console, choose **Elastic Load Balance** > **IP Address Groups** and copy the **ID** of the target IP address group.                                                                   |
      +------------------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.acl-status | String                | This parameter is mandatory when you configure an IP address blocklist or trustlist for a load balancer. Options:                                                                                                                                                                                          |
      |                              |                       |                                                                                                                                                                                                                                                                                                            |
      |                              |                       | -  **on**: Access control is enabled.                                                                                                                                                                                                                                                                      |
      |                              |                       | -  **off**: Access control is disabled.                                                                                                                                                                                                                                                                    |
      +------------------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.acl-type   | String                | This parameter is mandatory when you configure an IP address blocklist or trustlist for a load balancer. Options:                                                                                                                                                                                          |
      |                              |                       |                                                                                                                                                                                                                                                                                                            |
      |                              |                       | -  **black**: indicates a blocklist. The selected IP address group cannot access the load balancer.                                                                                                                                                                                                        |
      |                              |                       | -  **white**: indicates a trustlist. Only the selected IP address group can access the load balancer.                                                                                                                                                                                                      |
      +------------------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

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
      ingress-test  cce      *         121.**.**.**     80      10s
