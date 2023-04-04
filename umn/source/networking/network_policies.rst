:original_name: cce_10_0059.html

.. _cce_10_0059:

Network Policies
================

NetworkPolicy is a Kubernetes object used to restrict pod access. In CCE, by setting network policies, you can define ingress rules specifying the addresses to access pods or egress rules specifying the addresses pods can access. This is equivalent to setting up a firewall at the application layer to further ensure network security.

Network policies depend on the networking add-on of the cluster to which the policies apply.

By default, if a namespace does not have any policy, pods in the namespace accept traffic from any source and send traffic to any destination.

Network policy rules are classified into the following types:

-  **namespaceSelector**: selects particular namespaces for which all pods should be allowed as ingress sources or egress destinations.
-  **podSelector**: selects particular pods in the same namespace as the network policy which should be allowed as ingress sources or egress destinations.
-  **ipBlock**: selects particular IP blocks to allow as ingress sources or egress destinations. (Only egress rules support IP blocks.)

Notes and Constraints
---------------------

-  Only clusters that use the tunnel network model support network policies.

-  Network isolation is not supported for IPv6 addresses.

-  Network policies do not support egress rules except for clusters of v1.23 or later.

   Egress rules are supported only in the following operating systems:

   -  EulerOS 2.9: kernel version 4.18.0-147.5.1.6.h541.eulerosv2r9.x86_64
   -  CentOS 7.7: kernel version 3.10.0-1062.18.1.el7.x86_64
   -  EulerOS 2.5: kernel version 3.10.0-862.14.1.5.h591.eulerosv2r7.x86_64

-  If a cluster is upgraded to v1.23 in in-place mode, you cannot use egress rules because the node OS is not upgraded. In this case, reset the node.

Using Ingress Rules
-------------------

-  **Using podSelector to specify the access scope**

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: NetworkPolicy
      metadata:
        name: test-network-policy
        namespace: default
      spec:
        podSelector:                  # The rule takes effect for pods with the role=db label.
          matchLabels:
            role: db
        ingress:                      #This is an ingress rule.
        - from:
          - podSelector:              #Only traffic from the pods with the role=frontend label is allowed.
              matchLabels:
                role: frontend
          ports:                      #Only TCP can be used to access port 6379.
          - protocol: TCP
            port: 6379

   Diagram:


   .. figure:: /_static/images/en-us_image_0259557735.png
      :alt: **Figure 1** podSelector

      **Figure 1** podSelector

-  **Using namespaceSelector to specify the access scope**

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: NetworkPolicy
      metadata:
        name: test-network-policy
      spec:
        podSelector:                  # The rule takes effect for pods with the role=db label.
          matchLabels:
            role: db
        ingress:                      #This is an ingress rule.
        - from:
          - namespaceSelector:        # Only traffic from the pods in the namespace with the "project=myproject" label is allowed.
              matchLabels:
                project: myproject
          ports:                      #Only TCP can be used to access port 6379.
          - protocol: TCP
            port: 6379

   :ref:`Figure 2 <cce_10_0059__en-us_topic_0249851123_fig127351855617>` shows how namespaceSelector selects ingress sources.

   .. _cce_10_0059__en-us_topic_0249851123_fig127351855617:

   .. figure:: /_static/images/en-us_image_0259558489.png
      :alt: **Figure 2** namespaceSelector

      **Figure 2** namespaceSelector

Using Egress Rules
------------------

Egress supports not only podSelector and namespaceSelector, but also ipBlock.

.. note::

   Only clusters of version 1.23 or later support egress rules. Currently, only EulerOS 2.5, EulerOS 2.9, and CentOS 7.7 nodes are supported.

.. code-block::

   apiVersion: networking.k8s.io/v1
   kind: NetworkPolicy
   metadata:
     name: deny-client-a-via-except-cidr-egress-rule
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
           cidr: 172.16.0.16/16    # Allow access to this CIDR block.
           except:
           - 172.16.0.40/32        # This CIDR block cannot be accessed. This value must fall within the range specified by cidr.

Diagram:


.. figure:: /_static/images/en-us_image_0000001340138373.png
   :alt: **Figure 3** ipBlock

   **Figure 3** ipBlock

You can define ingress and egress in the same rule.

.. code-block::

   apiVersion: networking.k8s.io/v1
   kind: NetworkPolicy
   metadata:
     name: test-network-policy
     namespace: default
   spec:
     policyTypes:
     - Ingress
     - Egress
     podSelector:                  # The rule takes effect for pods with the role=db label.
       matchLabels:
         role: db
     ingress:                      # Ingress rule
     - from:
       - podSelector:              #Only traffic from the pods with the "role=frontend" label is allowed.
           matchLabels:
             role: frontend
       ports:                      #Only TCP can be used to access port 6379.
       - protocol: TCP
         port: 6379
     egress:                       # Egress rule
     - to:
       - podSelector:              # Only pods with the role=web label can be accessed.
           matchLabels:
             role: web

Diagram:


.. figure:: /_static/images/en-us_image_0000001287883210.png
   :alt: **Figure 4** Using both ingress and egress

   **Figure 4** Using both ingress and egress

Creating a Network Policy on the Console
----------------------------------------

#. Log in to the CCE console and access the cluster console.
#. Choose **Networking** in the navigation pane, click the **Network Policies** tab, and click **Create Network Policy** in the upper right corner.

   -  **Policy Name**: Specify a network policy name.

   -  **Namespace**: Select a namespace in which the network policy is applied.

   -  **Selector**: Enter a label, select the pod to be associated, and click **Add**. You can also click **Reference Workload Label** to reference the label of an existing workload.

   -  **Inbound Rule**: Click |image1| to add an inbound rule. For details about parameter settings, see :ref:`Table 1 <cce_10_0059__table166419994515>`.

      |image2|

      .. _cce_10_0059__table166419994515:

      .. table:: **Table 1** Adding an inbound rule

         +------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter        | Description                                                                                                                                                  |
         +==================+==============================================================================================================================================================+
         | Protocol & Port  | Select the protocol type and port. Currently, TCP and UDP are supported.                                                                                     |
         +------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Source Namespace | Select a namespace whose objects can be accessed. If this parameter is not specified, the source object belongs to the same namespace as the current policy. |
         +------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Source Pod Label | Allow access to the pods with this label, if not specified, all pods in the namespace can be accessed.                                                       |
         +------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+

   -  **Outbound Rule**: Click |image3| to add an outbound rule. For details about parameter settings, see :ref:`Table 1 <cce_10_0059__table166419994515>`.

      |image4|

      .. table:: **Table 2** Adding an outbound rule

         +------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter              | Description                                                                                                                                                                                                                                                                                                                                                                       |
         +========================+===================================================================================================================================================================================================================================================================================================================================================================================+
         | Protocol & Port        | Select the protocol type and port. Currently, TCP and UDP are supported. If this parameter is not specified, the protocol type is not limited.                                                                                                                                                                                                                                    |
         +------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Destination CIDR Block | Allows requests to be routed to a specified CIDR block (and not to the exception CIDR blocks). Separate the destination and exception CIDR blocks by vertical bars (|), and separate multiple exception CIDR blocks by commas (,). For example, 172.17.0.0/16|172.17.1.0/24,172.17.2.0/24 indicates that 172.17.0.0/16 is accessible, but not for 172.17.1.0/24 or 172.17.2.0/24. |
         +------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Destination Namespace  | Select a namespace whose objects can be accessed. If this parameter is not specified, the source object belongs to the same namespace as the current policy.                                                                                                                                                                                                                      |
         +------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Destination Pod Label  | Allow access to the pods with this label, if not specified, all pods in the namespace can be accessed.                                                                                                                                                                                                                                                                            |
         +------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **OK**.

.. |image1| image:: /_static/images/en-us_image_0000001251716033.png
.. |image2| image:: /_static/images/en-us_image_0000001533585325.png
.. |image3| image:: /_static/images/en-us_image_0000001533586881.png
.. |image4| image:: /_static/images/en-us_image_0000001482546084.png
