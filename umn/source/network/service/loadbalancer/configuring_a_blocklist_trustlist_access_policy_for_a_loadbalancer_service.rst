:original_name: cce_10_0831.html

.. _cce_10_0831:

Configuring a Blocklist/Trustlist Access Policy for a LoadBalancer Service
==========================================================================

When using a LoadBalancer Service, you can configure a trustlist or blocklist to specify the IP addresses that are allowed or denied to access a load balancer listener.

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
#. Choose **Services & Ingresses** in the navigation pane, click the **Services** tab, and click **Create Service** in the upper right corner.
#. Configure Service parameters.

   -  **Service Name**: Enter a service name, for example, **service-acl**.

   -  **Service Type**: Select **LoadBalancer**.

   -  **Service Affinity**: Select cluster level or node level as needed. For details about the differences, see :ref:`externalTrafficPolicy (Service Affinity) <cce_10_0249__section18134208069>`.

   -  **Selector**: Add a label and click **Confirm**. The Service will use this label to select pods. You can also click **Reference Workload Label** to use the label of an existing workload. In the dialog box that is displayed, select a workload and click **OK**.

   -  **Load Balancer**

      Select a load balancer to be accessed. Only load balancers in the same VPC as the cluster are supported. If no load balancer is available, click **Create Load Balancer** to create one on the ELB console. Alternatively, select **Auto create** to create a load balancer. For details about parameter settings, see :ref:`Table 1 <cce_10_0681__table026610571395>`.

   -  **Health Check**: defaults to **Global health check**. You can configure this parameter as needed.

   -  **Ports**

      -  **Protocol**: protocol used by the Service.
      -  **Service Port**: port used by the Service. The port number ranges from 1 to 65535.
      -  **Container Port**: listener port of the workload. For example, Nginx uses port 80 by default.

   -  **Access Control**

      -  **Allow all IP addresses**: No access control is configured.
      -  **Trustlist**: Only the selected IP address group can access the load balancer.
      -  **Blocklist**: The selected IP address group cannot access the load balancer.

#. Click **OK**.

Using kubectl
-------------

An example YAML file of a Service created using an existing load balancer is as follows:

.. code-block::

   apiVersion: v1
   kind: Service
   metadata:
     name: nginx
     annotations:
       kubernetes.io/elb.id: <your_elb_id>                    # Load balancer ID. Replace it with the actual value.
       kubernetes.io/elb.class: performance                   # Load balancer type
       kubernetes.io/elb.acl-id: <your_acl_id>               # ID of an IP address group for accessing a load balancer
       kubernetes.io/elb.acl-status: 'on'                  # Enable access control.
       kubernetes.io/elb.acl-type: 'white'                   # Trustlist for access control
   spec:
     selector:
        app: nginx
     ports:
     - name: service0
       port: 80
       protocol: TCP
       targetPort: 80
     type: LoadBalancer

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
