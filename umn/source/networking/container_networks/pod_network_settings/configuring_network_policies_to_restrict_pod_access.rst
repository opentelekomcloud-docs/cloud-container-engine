:original_name: cce_10_0059.html

.. _cce_10_0059:

Configuring Network Policies to Restrict Pod Access
===================================================

Network policies are designed by Kubernetes to restrict pod access. Like a firewall at the application layer, network policies enhance network security. The capabilities supported by network policies depend on the capabilities of the network add-ons of the cluster.

By default, if a namespace does not have any policy, pods in the namespace accept traffic from any source and send traffic to any destination.

The following selectors are available for network policies:

-  **namespaceSelector**: selects particular namespaces for which all pods should be allowed as ingress sources or egress destinations.
-  **podSelector**: selects particular pods in the same namespace as the network policy which should be allowed as ingress sources or egress destinations.
-  **ipBlock**: selects particular CIDR blocks to allow as ingress sources or egress destinations.

Relationships Between Network Policies and Cluster Types
--------------------------------------------------------

+-----------------------------------------------------------------+----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| Cluster Type                                                    | CCE Standard Cluster | CCE Standard Cluster                                                                                                                            | CCE Turbo Cluster                                                                                                                               |
+=================================================================+======================+=================================================================================================================================================+=================================================================================================================================================+
| Network Model                                                   | Tunnel Network       | VPC Network                                                                                                                                     | Cloud Native Network 2.0                                                                                                                        |
+-----------------------------------------------------------------+----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| Network policies                                                | Enabled by default   | Disabled by default (To use network policies, enable DataPlane V2 when creating a cluster.)                                                     | Disabled by default (To use network policies, enable DataPlane V2 when creating a cluster.)                                                     |
+-----------------------------------------------------------------+----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| Data plane implementation                                       | OpenvSwitch          | eBPF                                                                                                                                            | eBPF                                                                                                                                            |
+-----------------------------------------------------------------+----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| Cluster versions for ingress rules                              | All versions         | v1.27.16-r30, v1.28.15-r20, v1.29.13-r0, v1.30.10-r0, v1.31.6-r0, or later                                                                      | Clusters v1.34.3-r10 or later                                                                                                                   |
+-----------------------------------------------------------------+----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| Cluster versions for egress rules                               | v1.23 and later      |                                                                                                                                                 |                                                                                                                                                 |
+-----------------------------------------------------------------+----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| Selector for ingress rules                                      | namespaceSelector    | namespaceSelector                                                                                                                               | namespaceSelector                                                                                                                               |
|                                                                 |                      |                                                                                                                                                 |                                                                                                                                                 |
|                                                                 | podSelector          | podSelector                                                                                                                                     | podSelector                                                                                                                                     |
|                                                                 |                      |                                                                                                                                                 |                                                                                                                                                 |
|                                                                 |                      | ipBlock                                                                                                                                         | ipBlock                                                                                                                                         |
+-----------------------------------------------------------------+----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| Selector for egress rules                                       | namespaceSelector    |                                                                                                                                                 |                                                                                                                                                 |
|                                                                 |                      |                                                                                                                                                 |                                                                                                                                                 |
|                                                                 | podSelector          |                                                                                                                                                 |                                                                                                                                                 |
|                                                                 |                      |                                                                                                                                                 |                                                                                                                                                 |
|                                                                 | ipBlock              |                                                                                                                                                 |                                                                                                                                                 |
+-----------------------------------------------------------------+----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| Supported OS                                                    | EulerOS              | HCE OS 2.0 is supported.                                                                                                                        | HCE OS 2.0 is supported.                                                                                                                        |
|                                                                 |                      |                                                                                                                                                 |                                                                                                                                                 |
|                                                                 | HCE OS 2.0           | Clusters v1.28.15-r70, v1.29.15-r30, v1.30.14-r30, v1.31.10-r30, v1.32.6-r30, v1.33.5-r20, v1.34.1-r0, and later versions support Ubuntu 22.04. | Clusters v1.28.15-r70, v1.29.15-r30, v1.30.14-r30, v1.31.10-r30, v1.32.6-r30, v1.33.5-r20, v1.34.1-r0, and later versions support Ubuntu 22.04. |
+-----------------------------------------------------------------+----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| IPv6 network policies                                           | Not supported        | Not supported                                                                                                                                   | Supported                                                                                                                                       |
+-----------------------------------------------------------------+----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| Secure containers                                               | Not supported        | Not supported                                                                                                                                   | Not supported                                                                                                                                   |
+-----------------------------------------------------------------+----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| IPBlock scope                                                   | Not limited          | Subnets within the pod CIDR block, Service CIDR block, and node IP addresses                                                                    | Subnets within the pod CIDR block, Service CIDR block, and node IP addresses                                                                    |
+-----------------------------------------------------------------+----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| Limiting ClusterIP access through workload labels               | Not supported        | Supported                                                                                                                                       | Supported                                                                                                                                       |
+-----------------------------------------------------------------+----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| Limiting the internal cloud server CIDR block of 100.125.0.0/16 | Supported            | Supported                                                                                                                                       | Supported                                                                                                                                       |
+-----------------------------------------------------------------+----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| SCTP                                                            | Not supported        | Supported                                                                                                                                       | Supported                                                                                                                                       |
+-----------------------------------------------------------------+----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| Always allowing access to pods on a node from other nodes       | Supported            | Supported                                                                                                                                       | Supported                                                                                                                                       |
+-----------------------------------------------------------------+----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
| Configuring EndPort in network policies                         | Not supported        | Supported                                                                                                                                       | Supported                                                                                                                                       |
+-----------------------------------------------------------------+----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+

.. note::

   -  If you upgrade a CCE standard cluster with a tunnel network to a version that supports egress rules in in-place mode, the rules will not work because the node OS is not upgraded. In this case, reset the node.
   -  When a network policy is enabled for a cluster with a tunnel network, a pod's source IP address is embedded in the optional field of packets it sends to any Service CIDR block. This enables the configuration of network policy rules on the destination pod, taking into account the source IP address of the pod.

Using Ingress Rules Through YAML
--------------------------------

-  **Scenario 1: Configure a network policy to allow only pods with specified labels to access the target pod.**


   .. figure:: /_static/images/en-us_image_0000002661849369.png
      :alt: **Figure 1** podSelector

      **Figure 1** podSelector

   The pod labeled with **role=db** only permits access to its port 6379 from pods labeled with **role=frontend**. To achieve this, take the following steps:

   #. Create the **access-ingress1.yaml** file.

      .. code-block::

         vim access-ingress1.yaml

      File content:

      .. code-block::

         apiVersion: networking.k8s.io/v1
         kind: NetworkPolicy
         metadata:
           name: access-ingress1
           namespace: default
         spec:
           podSelector:                  # The rule applies only to pods labeled with role=db.
             matchLabels:
               role: db
           ingress:                      # This is an ingress rule.
           - from:
             - podSelector:              # Allow access only from pods labeled with role=frontend.
                 matchLabels:
                   role: frontend
             ports:                      # Only TCP can be used to access port 6379.
             - protocol: TCP
               port: 6379

   #. Run the following command to create the network policy defined in the **access-ingress1.yaml** file:

      .. code-block::

         kubectl apply -f access-ingress1.yaml

      Expected output:

      .. code-block::

         networkpolicy.networking.k8s.io/access-ingress1 created

-  **Scenario 2: Configure a network policy to allow only pods in a specific namespace to access the target pod.**


   .. figure:: /_static/images/en-us_image_0000002631690068.png
      :alt: **Figure 2** namespaceSelector

      **Figure 2** namespaceSelector

   The pod labeled with **role=db** only permits access to its port 6379 from pods in the namespace labeled with **project=myproject**. To achieve this, take the following steps:

   #. Create the **access-ingress2.yaml** file.

      .. code-block::

         vim access-ingress2.yaml

      File content:

      .. code-block::

         apiVersion: networking.k8s.io/v1
         kind: NetworkPolicy
         metadata:
           name: access-ingress2
         spec:
           podSelector:                  # The rule applies only to pods labeled with role=db.
             matchLabels:
               role: db
           ingress:                      # This is an ingress rule.
           - from:
             - namespaceSelector:        # Allow access only from pods in namespaces labeled with project=myproject.
                 matchLabels:
                   project: myproject
             ports:                      # Only TCP can be used to access port 6379.
             - protocol: TCP
               port: 6379

   #. Run the following command to create the network policy defined in the **access-ingress2.yaml** file:

      .. code-block::

         kubectl apply -f access-ingress2.yaml

      Expected output:

      .. code-block::

         networkpolicy.networking.k8s.io/access-ingress2 created

-  **Scenario 3: Configure a network policy to allow only pods with specific labels in a specific namespace to access the target pod.**


   .. figure:: /_static/images/en-us_image_0000002631530176.png
      :alt: **Figure 3** Using both podSelector and namespaceSelector

      **Figure 3** Using both podSelector and namespaceSelector

   The pod labeled with **role=db** only allows access to its port 6379 from pods with label **role=frontend** in the namespace labeled with **project=myproject**. To achieve this, take the following steps:

   #. Create the **access-ingress3.yaml** file.

      .. code-block::

         vim access-ingress3.yaml

      File content:

      .. code-block::

         apiVersion: networking.k8s.io/v1
         kind: NetworkPolicy
         metadata:
           name: access-ingress3
         spec:
           podSelector:                  # The rule applies only to pods labeled with role=db.
             matchLabels:
               role: db
           ingress:                      # This is an ingress rule.
           - from:
             - namespaceSelector:        # Allow access only from pods in namespaces labeled with project=myproject.
                 matchLabels:
                   project: myproject
               podSelector:              # Allow access only from pods labeled with role=frontend.
                 matchLabels:
                   role: frontend
             ports:                      # Only TCP can be used to access port 6379.
             - protocol: TCP
               port: 6379

   #. Run the following command to create the network policy defined in the **access-ingress3.yaml** file:

      .. code-block::

         kubectl apply -f access-ingress3.yaml

      Expected output:

      .. code-block::

         networkpolicy.networking.k8s.io/access-ingress3 created

Using Egress Rules Through YAML
-------------------------------

-  **Scenario 1: Controlled by a preset network policy, pods can only access specific addresses.**


   .. figure:: /_static/images/en-us_image_0000002631530174.png
      :alt: **Figure 4** ipBlock

      **Figure 4** ipBlock

   The pods labeled **role=db** only allow access to 172.16.0.0/16, excluding 172.16.0.40/32. To achieve this, take the following steps:

   #. Create the **access-egress1.yaml** file.

      .. code-block::

         vim access-egress1.yaml

      File content:

      .. code-block::

         apiVersion: networking.k8s.io/v1
         kind: NetworkPolicy
         metadata:
           name: access-egress1
           namespace: default
         spec:
           policyTypes:                  # This policy type must be specified for egress rules.
             - Egress
           podSelector:                  # The rule applies only to pods labeled with role=db.
             matchLabels:
               role: db
           egress:                       # This is an egress rule.
           - to:
             - ipBlock:
                 cidr: 172.16.0.0/16    # Allows access to this CIDR block in the outbound direction.
                 except:
                 - 172.16.0.40/32        # Blocks access to this CIDR block, which is in the range specified by the cidr parameter.

   #. Run the following command to create the network policy defined in the **access-egress1.yaml** file:

      .. code-block::

         kubectl apply -f access-egress1.yaml

      Expected output:

      .. code-block::

         networkpolicy.networking.k8s.io/access-egress1 created

-  **Scenario 2: Controlled by a preset network policy, a pod can only be accessed by pods with specific labels, while this pod itself can only access specific pods.**


   .. figure:: /_static/images/en-us_image_0000002631530172.png
      :alt: **Figure 5** Using both ingress and egress

      **Figure 5** Using both ingress and egress

   The pod labeled with **role=db** only permits access to its port 6379 from pods labeled with **role=frontend**, and this pod can only access the pods labeled with **role=web**. You can use the same rule to configure both ingress and egress in a network policy. To achieve this, take the following steps:

   #. Create the **access-egress2.yaml** file.

      .. code-block::

         vim access-egress2.yaml

      File content:

      .. code-block::

         apiVersion: networking.k8s.io/v1
         kind: NetworkPolicy
         metadata:
           name: access-egress2
           namespace: default
         spec:
           policyTypes:
           - Ingress
           - Egress
           podSelector:                  # The rule applies only to pods labeled with role=db.
             matchLabels:
               role: db
           ingress:                      # This is an ingress rule.
           - from:
             - podSelector:              # Allow access only from pods labeled with role=frontend.
                 matchLabels:
                   role: frontend
             ports:                      # Only TCP can be used to access port 6379.
             - protocol: TCP
               port: 6379
           egress:                       # This is an egress rule.
           - to:
             - podSelector:              # The rule takes effect for pods with the role=web label.
                 matchLabels:
                   role: web

   #. Run the following command to create the network policy defined in the **access-egress2.yaml** file:

      .. code-block::

         kubectl apply -f access-egress2.yaml

      Expected output:

      .. code-block::

         networkpolicy.networking.k8s.io/access-egress2 created

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

         +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                        |
         +===================================+====================================================================================================================================================================================================================================================================================================================================================================================================================================================+
         | Protocol & Port                   | Select the protocol type and port. Currently, TCP and UDP are supported.                                                                                                                                                                                                                                                                                                                                                                           |
         +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Source CIDR Block                 | For clusters v1.27.16-r10, v1.28.15-r0, v1.29.10-r0, v1.30.6-r0, or later versions with DataPlane V2 enabled, you can configure the source CIDR block.                                                                                                                                                                                                                                                                                             |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
         |                                   | The specified source CIDR block allows traffic from a destination CIDR block (multiple exception CIDR blocks can be specified). Separate the destination and exception CIDR blocks using a vertical bar (|). If there are multiple exception CIDR blocks, separate them using commas (,). For example, 172.17.0.0/16|172.17.1.0/24,172.17.2.0/24 indicates that 172.17.0.0/16 is accessible, but 172.17.1.0/24 and 172.17.2.0/24 are inaccessible. |
         +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Source Namespace                  | Select a namespace whose objects can be accessed. If this parameter is not specified, the object belongs to the same namespace as the current policy.                                                                                                                                                                                                                                                                                              |
         +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Source Pod Label                  | Allow accessing the pods with this label. If this parameter is not specified, all pods in the namespace can be accessed.                                                                                                                                                                                                                                                                                                                           |
         +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   -  **Outbound Rule**: Click |image3| to add an outbound rule. For details about parameter settings, see :ref:`Table 2 <cce_10_0059__table940510264284>`.

      |image4|

      .. _cce_10_0059__table940510264284:

      .. table:: **Table 2** Adding an outbound rule

         +------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter              | Description                                                                                                                                                                                                                                                                                                                                                                                                      |
         +========================+==================================================================================================================================================================================================================================================================================================================================================================================================================+
         | Protocol & Port        | Select the protocol type and port. Currently, TCP and UDP are supported. If this parameter is not specified, the protocol type is not limited.                                                                                                                                                                                                                                                                   |
         +------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Destination CIDR Block | Allow requests to be routed to a specified CIDR block (and not to the exception CIDR blocks). Separate the destination and exception CIDR blocks using a vertical bar (|). If there are multiple exception CIDR blocks, separate them using commas (,). For example, 172.17.0.0/16|172.17.1.0/24,172.17.2.0/24 indicates that 172.17.0.0/16 is accessible, but 172.17.1.0/24 and 172.17.2.0/24 are inaccessible. |
         +------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Destination Namespace  | Select a namespace whose objects can be accessed. If this parameter is not specified, the object belongs to the same namespace as the current policy.                                                                                                                                                                                                                                                            |
         +------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Destination Pod Label  | Allow accessing the pods with this label. If this parameter is not specified, all pods in the namespace can be accessed.                                                                                                                                                                                                                                                                                         |
         +------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **OK**.

.. |image1| image:: /_static/images/en-us_image_0000002662009309.png
.. |image2| image:: /_static/images/en-us_image_0000002662009303.png
.. |image3| image:: /_static/images/en-us_image_0000002661849367.png
.. |image4| image:: /_static/images/en-us_image_0000002661849365.png
