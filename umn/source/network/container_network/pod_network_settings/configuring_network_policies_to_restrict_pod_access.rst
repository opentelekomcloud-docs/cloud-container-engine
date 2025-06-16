:original_name: cce_10_0059.html

.. _cce_10_0059:

Configuring Network Policies to Restrict Pod Access
===================================================

Network policies are designed by Kubernetes to restrict pod access. It is equivalent to a firewall at the application layer to enhance network security. The capabilities supported by network policies depend on the capabilities of the network add-ons of the cluster.

By default, if a namespace does not have any policy, pods in the namespace accept traffic from any source and send traffic to any destination.

The following selectors are available for network policies:

-  **namespaceSelector**: selects particular namespaces for which all pods should be allowed as ingress sources or egress destinations.
-  **podSelector**: selects particular pods in the same namespace as the network policy which should be allowed as ingress sources or egress destinations.
-  **IPBlock**: selects particular IP blocks to allow as ingress sources or egress destinations.

Relationships Between Network Policies and Cluster Types
--------------------------------------------------------

+--------------------------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------+
| Cluster Type                                                 | CCE Standard Cluster  | CCE Turbo Cluster                                                                           |
+==============================================================+=======================+=============================================================================================+
| Network Model                                                | Tunnel                | Cloud Native Network 2.0                                                                    |
+--------------------------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------+
| NetworkPolicy                                                | Enabled by default    | Disabled by default (To use network policies, enable DataPlane V2 when creating a cluster.) |
+--------------------------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------+
| Data plane implementation                                    | OpenvSwitch           | eBPF                                                                                        |
+--------------------------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------+
| Cluster version for inbound rules                            | All versions          | v1.27.16-r10, v1.28.15-r0, v1.29.10-r0, v1.30.6-r0, or later                                |
+--------------------------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------+
| Cluster version for outbound rules                           | v1.23 and later       |                                                                                             |
+--------------------------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------+
| Selector for inbound rules                                   | namespaceSelector     | namespaceSelector                                                                           |
|                                                              |                       |                                                                                             |
|                                                              | podSelector           | podSelector                                                                                 |
|                                                              |                       |                                                                                             |
|                                                              |                       | IPBlock                                                                                     |
+--------------------------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------+
| Selector for outbound rules                                  | namespaceSelector     |                                                                                             |
|                                                              |                       |                                                                                             |
|                                                              | podSelector           |                                                                                             |
|                                                              |                       |                                                                                             |
|                                                              | IPBlock               |                                                                                             |
+--------------------------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------+
| Supported OS                                                 | EulerOS               | HCE OS 2.0                                                                                  |
|                                                              |                       |                                                                                             |
|                                                              | HCE 2.0               |                                                                                             |
+--------------------------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------+
| IPv6 network policies                                        | Not supported         | Supported                                                                                   |
+--------------------------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------+
| Secure containers                                            | Not supported         | Not supported                                                                               |
+--------------------------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------+
| IPBlock scope                                                | Not limited           | The CIDR blocks and node IP addresses in the container CIDR block cannot be configured.     |
+--------------------------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------+
| Limit ClusterIP access through workload labels               | Not supported         | Supported                                                                                   |
+--------------------------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------+
| Limit the internal cloud server CIDR block of 100.125.0.0/16 | Supported             | Not supported                                                                               |
+--------------------------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------+
| SCTP                                                         | Not supported         | Not supported                                                                               |
+--------------------------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------+
| Always allow access to pods on a node from other nodes       | Supported             | Supported                                                                                   |
+--------------------------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------+
| Configure EndPort in network policies                        | Not supported         | Not supported                                                                               |
+--------------------------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------+

.. note::

   -  CCE DataPlane V2 is released with restrictions. To use this feature, submit a service ticket to CCE.
   -  Secure containers (such as Kata as the container runtime) are not supported by network policies.
   -  If you upgrade a CCE standard cluster with a tunnel network to a version that supports egress rules in in-place mode, the rules will not work because the node OS is not upgraded. In this case, reset the node.
   -  When a network policy is enabled for a cluster that uses a tunnel network, the source IP address of a pod accessing the CIDR block of a Service will be recorded in the optional field of the reported IP address data. This enables the configuration of network policy rules on the destination pod, taking into account the source IP address of the pod.

Using Ingress Rules Through YAML
--------------------------------

-  **Scenario 1: Use a network policy to limit access to a pod to only pods with specific labels.**


   .. figure:: /_static/images/en-us_image_0000002218820158.png
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


   .. figure:: /_static/images/en-us_image_0000002218660286.png
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

.. note::

   The clusters of v1.23 or later using a tunnel network support egress rules. Only nodes running EulerOS 2.9 or HCE OS 2.0 are supported.

   Egress rules are only supported by CCE Turbo clusters of v1.27.16-r10, v1.28.15-r0, v1.29.10-r0, v1.30.6-r0, or later with DataPlane V2 enabled. Additionally, nodes in these clusters must only run HCE OS 2.0.

-  **Scenario 1: Use a network policy to limit a pod's access to specific addresses.**


   .. figure:: /_static/images/en-us_image_0000002253779941.png
      :alt: **Figure 3** IPBlock

      **Figure 3** IPBlock

   The pod labeled with **role=db** only permits access to the 172.16.0.0/16 CIDR block, excluding 172.16.0.40/32 within it. To do so, perform the following operations:

   #. Create the **access-demo3.yaml** file.

      .. code-block::

         vim access-demo3.yaml

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
                 cidr:172.16.0.0/16    # Allow access to this CIDR block in the outbound direction.
                 except:
                 - 172.16.0.40/32        # Block access to this address in the CIDR block.

   #. Run the following command to create the network policy based on the **access-demo3.yaml** file:

      .. code-block::

         kubectl apply -f access-demo3.yaml

      Expected output:

      .. code-block::

         networkpolicy.networking.k8s.io/access-demo3 created

-  **Scenario 2: Use a network policy to limit access to a pod to only pods with specific labels and this pod can only access specific pods.**


   .. figure:: /_static/images/en-us_image_0000002253779949.png
      :alt: **Figure 4** Using both ingress and egress

      **Figure 4** Using both ingress and egress

   The pod labeled with **role=db** only permits access to its port 6379 from pods labeled with **role=frontend**, and this pod can only access the pods labeled with **role=web**. You can use the same rule to configure both ingress and egress in a network policy. To do so, perform the following operations:

   #. Create the **access-demo4.yaml** file.

      .. code-block::

         vim access-demo4.yaml

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

         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                               |
         +===================================+===========================================================================================================================================================================================================================================================================================================================================================================================================================================+
         | Protocol & Port                   | Select the protocol type and port. Currently, TCP and UDP are supported.                                                                                                                                                                                                                                                                                                                                                                  |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Source CIDR Block                 | For clusters of v1.27.16-r10, v1.28.15-r0, v1.29.10-r0, v1.30.6-r0, or later versions with DataPlane V2 enabled, you can configure the source CIDR block.                                                                                                                                                                                                                                                                                 |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                           |
         |                                   | The specified source CIDR block allows traffic from a destination CIDR block (multiple exception CIDR blocks can be specified). Separate the destination and exception CIDR blocks using a vertical bar (|). If there are multiple exception CIDR blocks, separate them using commas (,). For example, 172.17.0.0/16|172.17.1.0/24,172.17.2.0/24 indicates that 172.17.0.0/16 is accessible, but not for 172.17.1.0/24 and 172.17.2.0/24. |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Source Namespace                  | Select a namespace whose objects can be accessed. If this parameter is not specified, the object belongs to the same namespace as the current policy.                                                                                                                                                                                                                                                                                     |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Source Pod Label                  | Allow accessing the pods with this label. If this parameter is not specified, all pods in the namespace can be accessed.                                                                                                                                                                                                                                                                                                                  |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   -  **Outbound Rule**: Click |image3| to add an outbound rule. For details about parameter settings, see :ref:`Table 2 <cce_10_0059__table940510264284>`.

      |image4|

      .. _cce_10_0059__table940510264284:

      .. table:: **Table 2** Adding an outbound rule

         +------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter              | Description                                                                                                                                                                                                                                                                                                                                                                                              |
         +========================+==========================================================================================================================================================================================================================================================================================================================================================================================================+
         | Protocol & Port        | Select the protocol type and port. Currently, TCP and UDP are supported. If this parameter is not specified, the protocol type is not limited.                                                                                                                                                                                                                                                           |
         +------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Destination CIDR Block | Allows requests to be routed to a specified CIDR block (and not to the exception CIDR blocks). Separate the destination and exception CIDR blocks using a vertical bar (|). If there are multiple exception CIDR blocks, separate them using commas (,). For example, 172.17.0.0/16|172.17.1.0/24,172.17.2.0/24 indicates that 172.17.0.0/16 is accessible, but not for 172.17.1.0/24 and 172.17.2.0/24. |
         +------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Destination Namespace  | Select a namespace whose objects can be accessed. If this parameter is not specified, the object belongs to the same namespace as the current policy.                                                                                                                                                                                                                                                    |
         +------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Destination Pod Label  | Allow accessing the pods with this label. If this parameter is not specified, all pods in the namespace can be accessed.                                                                                                                                                                                                                                                                                 |
         +------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **OK**.

.. |image1| image:: /_static/images/en-us_image_0000002253620049.png
.. |image2| image:: /_static/images/en-us_image_0000002253620033.png
.. |image3| image:: /_static/images/en-us_image_0000002218660298.png
.. |image4| image:: /_static/images/en-us_image_0000002218660294.png
