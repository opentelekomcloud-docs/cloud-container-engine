:original_name: cce_10_0059.html

.. _cce_10_0059:

Configuring Network Policies to Restrict Pod Access
===================================================

Network policies are designed by Kubernetes to restrict pod access. It is equivalent to a firewall at the application layer to enhance network security. The capabilities supported by network policies depend on the capabilities of the network add-ons of the cluster.

By default, if a namespace does not have any policy, pods in the namespace accept traffic from any source and send traffic to any destination.

Network policies are classified into the following types:

-  **namespaceSelector**: selects particular namespaces for which all pods should be allowed as ingress sources.
-  **podSelector**: selects particular pods in the same namespace as the network policy which should be allowed as ingress sources.
-  **IPBlock**: selects particular IP blocks to allow as ingress sources. (Only egress support IP blocks.)

Notes and Constraints
---------------------

-  Only clusters that use the tunnel network model support network policies. Network policies are classified into the following types:

   -  Ingress: All versions support this type.
   -  Egress: Only the following OSs and cluster versions support egress rules.

      +-----------------------+-----------------------+-------------------------------------------+
      | OS                    | Cluster Version       | Verified Kernel Version                   |
      +=======================+=======================+===========================================+
      | EulerOS 2.9           | v1.23 or later        | 4.18.0-147.5.1.6.h541.eulerosv2r9.x86_64  |
      |                       |                       |                                           |
      |                       |                       | 4.18.0-147.5.1.6.h766.eulerosv2r9.x86_64  |
      |                       |                       |                                           |
      |                       |                       | 4.18.0-147.5.1.6.h998.eulerosv2r9.x86_64  |
      |                       |                       |                                           |
      |                       |                       | 4.18.0-147.5.1.6.h1305.eulerosv2r9.x86_64 |
      +-----------------------+-----------------------+-------------------------------------------+
      | HCE OS 2.0            | v1.25 or later        | 5.10.0-60.18.0.50.r865_35.hce2.x86_64     |
      |                       |                       |                                           |
      |                       |                       | 5.10.0-182.0.0.95.r1941_123.hce2.x86_64   |
      +-----------------------+-----------------------+-------------------------------------------+

-  Network isolation is not supported for IPv6 addresses.
-  If you upgrade a CCE cluster to a version that supports egress rules in in-place mode, the rules will not work because the node OS is not upgraded. In this case, reset the node.

Using Ingress Rules Through YAML
--------------------------------

-  **Scenario 1: Use a network policy to limit access to a pod to only pods with specific labels.**


   .. figure:: /_static/images/en-us_image_0000002065638906.png
      :alt: **Figure 1** podSelector

      **Figure 1** podSelector

   The pod labeled with **role=db** only permits access to its port 6379 from pods labeled with **role=frontend**. To do so, perform the following operations:

   #. Create the **access-demo1.yaml** file.

      .. code-block::

         vim access-demo1.yaml

      File content:

      .. code-block::

         apiVersion: networking.k8s.io/v1
         kind: NetworkPolicy
         metadata:
           name: access-demo1
           namespace: default
         spec:
           podSelector:                  # The rule takes effect for pods with the role=db label.
             matchLabels:
               role: db
           ingress:                      # This is an ingress rule.
           - from:
             - podSelector:              # Only allow the access of the pods labeled with role=frontend.
                 matchLabels:
                   role: frontend
             ports:                      # Only TCP can be used to access port 6379.
             - protocol: TCP
               port: 6379

   #. Run the following command to create the network policy based on the **access-demo1.yaml** file:

      .. code-block::

         kubectl apply -f access-demo1.yaml

      Expected output:

      .. code-block::

         networkpolicy.networking.k8s.io/access-demo1 created

-  **Scenario 2: Use a network policy to limit access to a pod to only pods in a specific namespace.**


   .. figure:: /_static/images/en-us_image_0000002101678937.png
      :alt: **Figure 2** namespaceSelector

      **Figure 2** namespaceSelector

   The pod labeled with **role=db** only permits access to its port 6379 from pods in the namespace labeled with **project=myproject**. To do so, perform the following operations:

   #. Create the **access-demo2.yaml** file.

      .. code-block::

         vim access-demo2.yaml

      File content:

      .. code-block::

         apiVersion: networking.k8s.io/v1
         kind: NetworkPolicy
         metadata:
           name: access-demo2
         spec:
           podSelector:                  # The rule takes effect for pods with the role=db label.
             matchLabels:
               role: db
           ingress:                      # This is an ingress rule.
           - from:
             - namespaceSelector:        # Only allow the access of the pods in the namespace labeled with project=myproject.
                 matchLabels:
                   project: myproject
             ports:                      # Only TCP can be used to access port 6379.
             - protocol: TCP
               port: 6379

   #. Run the following command to create the network policy based on the **access-demo2.yaml** file:

      .. code-block::

         kubectl apply -f access-demo2.yaml

      Expected output:

      .. code-block::

         networkpolicy.networking.k8s.io/access-demo2 created

Using Egress Rules Through YAML
-------------------------------

Egress supports podSelector, namespaceSelector, and IPBlock.

.. note::

   Only clusters of v1.23 or later support egress rules. Only nodes running EulerOS 2.9 or HCE OS 2.0 are supported.

-  **Scenario 1: Use a network policy to limit a pod's access to specific addresses.**


   .. figure:: /_static/images/en-us_image_0000002065638890.png
      :alt: **Figure 3** IPBlock

      **Figure 3** IPBlock

   The pod labeled with **role=db** only permits access to the 172.16.0.16/16 CIDR block, excluding 172.16.0.40/32 within it. To do so, perform the following operations:

   #. Create the **access-demo3.yaml** file.

      .. code-block::

         vim access-demo2.yaml

      File content:

      .. code-block::

         apiVersion: networking.k8s.io/v1
         kind: NetworkPolicy
         metadata:
           name: access-demo3
           namespace: default
         spec:
           policyTypes:                  # Must be specified for an egress rule.
             - Egress
           podSelector:                  # The rule takes effect for pods with the role=db label.
             matchLabels:
               role: db
           egress:                       # Egress rule
           - to:
             - ipBlock:
                 cidr: 172.16.0.16/16    # Allow access to this CIDR block in the outbound direction.
                 except:
                 - 172.16.0.40/32        # Block access to this address in the CIDR block.

   #. Run the following command to create the network policy based on the **access-demo3.yaml** file:

      .. code-block::

         kubectl apply -f access-demo3.yaml

      Expected output:

      .. code-block::

         networkpolicy.networking.k8s.io/access-demo3 created

-  **Scenario 2: Use a network policy to limit access to a pod to only pods with specific labels and this pod can only access specific pods.**


   .. figure:: /_static/images/en-us_image_0000002065638898.png
      :alt: **Figure 4** Using both ingress and egress

      **Figure 4** Using both ingress and egress

   The pod labeled with **role=db** only permits access to its port 6379 from pods labeled with **role=frontend**, and this pod can only access the pods labeled with **role=web**. You can use the same rule to configure both ingress and egress in a network policy. To do so, perform the following operations:

   #. Create the **access-demo4.yaml** file.

      .. code-block::

         vim access-demo2.yaml

      File content:

      .. code-block::

         apiVersion: networking.k8s.io/v1
         kind: NetworkPolicy
         metadata:
           name: access-demo4
           namespace: default
         spec:
           policyTypes:
           - Ingress
           - Egress
           podSelector:                  # The rule takes effect for pods with the role=db label.
             matchLabels:
               role: db
           ingress:                      # This is an ingress rule.
           - from:
             - podSelector:              # Only allow the access of the pods labeled with role=frontend.
                 matchLabels:
                   role: frontend
             ports:                      # Only TCP can be used to access port 6379.
             - protocol: TCP
               port: 6379
           egress:                       # Egress rule
           - to:
             - podSelector:              # Only pods with the role=web label can be accessed.
                 matchLabels:
                   role: web

   #. Run the following command to create the network policy based on the **access-demo4.yaml** file:

      .. code-block::

         kubectl apply -f access-demo4.yaml

      Expected output:

      .. code-block::

         networkpolicy.networking.k8s.io/access-demo4 created

Creating a Network Policy on the Console
----------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Choose **Policies** in the navigation pane, click the **Network Policies** tab, and click **Create Network Policy** in the upper right corner.

   -  **Policy Name**: Specify a network policy name.

   -  **Namespace**: Select a namespace in which the network policy is applied.

   -  **Selector**: Enter a label, select the pod to be associated, and click **Add**. You can also click **Reference Workload Label** to use the label of an existing workload.

   -  **Inbound Rule**: Click |image1| to add an inbound rule. For details about parameter settings, see :ref:`Table 1 <cce_10_0059__table166419994515>`.

      |image2|

      .. _cce_10_0059__table166419994515:

      .. table:: **Table 1** Adding an inbound rule

         +------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter        | Description                                                                                                                                           |
         +==================+=======================================================================================================================================================+
         | Protocol & Port  | Select the protocol type and port. Currently, TCP and UDP are supported.                                                                              |
         +------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Source Namespace | Select a namespace whose objects can be accessed. If this parameter is not specified, the object belongs to the same namespace as the current policy. |
         +------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Source Pod Label | Allow accessing the pods with this label. If this parameter is not specified, all pods in the namespace can be accessed.                              |
         +------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------+

   -  **Outbound Rule**: Click |image3| to add an outbound rule. For details about parameter settings, see :ref:`Table 1 <cce_10_0059__table166419994515>`.

      |image4|

      .. table:: **Table 2** Adding an outbound rule

         +------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter              | Description                                                                                                                                                                                                                                                                                                                                                                        |
         +========================+====================================================================================================================================================================================================================================================================================================================================================================================+
         | Protocol & Port        | Select the protocol type and port. Currently, TCP and UDP are supported. If this parameter is not specified, the protocol type is not limited.                                                                                                                                                                                                                                     |
         +------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Destination CIDR Block | Allows requests to be routed to a specified CIDR block (and not to the exception CIDR blocks). Separate the destination and exception CIDR blocks by vertical bars (|), and separate multiple exception CIDR blocks by commas (,). For example, 172.17.0.0/16|172.17.1.0/24,172.17.2.0/24 indicates that 172.17.0.0/16 is accessible, but not for 172.17.1.0/24 and 172.17.2.0/24. |
         +------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Destination Namespace  | Select a namespace whose objects can be accessed. If this parameter is not specified, the object belongs to the same namespace as the current policy.                                                                                                                                                                                                                              |
         +------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Destination Pod Label  | Allow accessing the pods with this label. If this parameter is not specified, all pods in the namespace can be accessed.                                                                                                                                                                                                                                                           |
         +------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **OK**.

.. |image1| image:: /_static/images/en-us_image_0000002101678953.png
.. |image2| image:: /_static/images/en-us_image_0000002065638886.png
.. |image3| image:: /_static/images/en-us_image_0000002065480558.png
.. |image4| image:: /_static/images/en-us_image_0000002065480554.png
