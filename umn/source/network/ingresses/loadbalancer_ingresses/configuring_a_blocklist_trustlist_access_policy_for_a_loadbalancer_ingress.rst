:original_name: cce_10_0832.html

.. _cce_10_0832:

Configuring a Blocklist/Trustlist Access Policy for a LoadBalancer Ingress
==========================================================================

You can add IP addresses to a trustlist or blocklist to control access to a listener of a LoadBalancer ingress.

-  Trustlist: Only the IP addresses in the list can access the listener.
-  Blocklist: The IP addresses in the list are not allowed to access the listener.

Prerequisites
-------------

-  A Kubernetes cluster is available and the cluster version meets the following requirements:

   -  v1.23: v1.23.12-r0 or later
   -  v1.25: v1.25.7-r0 or later
   -  v1.27: v1.27.4-r0 or later
   -  v1.28: v1.28.2-r0 or later
   -  Other clusters of later versions

-  An IP address group has been created on the ELB console. For details, see .

Using the CCE Console
---------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. Choose **Services & Ingresses** in the navigation pane, click the **Ingresses** tab, and click **Create Ingress** in the upper right corner.

#. Configure access control parameters for the ingress.

   -  **Allow all IP addresses**: No access control is configured.
   -  **Trustlist**: Only the selected IP address group can access the load balancer.
   -  **Blocklist**: The selected IP address group cannot access the load balancer.

   For details about how to configure other parameters, see :ref:`Creating a LoadBalancer Ingress on the Console <cce_10_0251>`.

#. Click **OK**.

Using kubectl
-------------

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
       kubernetes.io/elb.acl-status: 'on'                  # Enable access control.
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
                 number: 8080             # Replace 8080 with the port number of your target Service.
           property:
             ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
           pathType: ImplementationSpecific
     ingressClassName: cce

.. table:: **Table 1** Annotations for ELB access control

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
