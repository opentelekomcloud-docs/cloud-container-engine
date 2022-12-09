:original_name: cce_01_0059.html

.. _cce_01_0059:

Network Policies
================

As the service logic becomes increasingly complex, many applications require network calls between modules. Traditional external firewalls or application-based firewalls cannot meet the requirements. Network policies are urgently needed between modules, service logic layers, or functional teams in a large cluster.

CCE has enhanced the Kubernetes-based network policy feature, allowing network isolation in a cluster by configuring network policies. This means that a firewall can be set between pods.

For example, to make a payment system accessible only to specified components for security purposes, you can configure network policies.

Notes and Constraints
---------------------

-  Only clusters that use the **tunnel network model** support network policies.

-  Network isolation is not supported for IPv6 addresses.

-  Network policies do not support egress rules except for clusters of v1.23 or later.

   Egress rules are supported only in the following operating systems:

   -  EulerOS 2.9: kernel version 4.18.0-147.5.1.6.h541.eulerosv2r9.x86_64
   -  CentOS 7.7: kernel version 3.10.0-1062.18.1.el7.x86_64
   -  EulerOS 2.5: kernel version 3.10.0-862.14.1.5.h591.eulerosv2r7.x86_64

-  If a cluster is upgraded to v1.23 in in-place mode, you cannot use egress rules because the node OS is not upgraded. In this case, reset the node.

Precautions
-----------

If no network policies have been configured for a workload, such as **workload-1**, other workloads in the same cluster can access **workload-1**.

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

   :ref:`Figure 2 <cce_01_0059__en-us_topic_0249851123_fig127351855617>` shows how namespaceSelector selects ingress sources.

   .. _cce_01_0059__en-us_topic_0249851123_fig127351855617:

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

Adding a Network Policy on the Console
--------------------------------------

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Network**. On the **Network Policies** tab page, click **Create Network Policy**.

   -  **Network Policy Name**: Specify a network policy name.

   -  **Cluster Name**: Select a cluster to which the network policy belongs.

   -  **Namespace**: Select a namespace in which the network policy is applied.

   -  **Workload**

      Click **Select Workload**. In the dialog box displayed, select a workload for which the network policy is to be created, for example, **workload-1**. Then, click **OK**.

   -  **Rules**: Click **Add Rule**, set the parameters listed in :ref:`Table 1 <cce_01_0059__table26919378234>`, and click **OK**.

      .. _cce_01_0059__table26919378234:

      .. table:: **Table 1** Parameters for adding a rule

         +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                    |
         +===================================+================================================================================================================================================================+
         | Direction                         | Only **Inbound** is supported, indicating that the whitelisted workloads access the current workload (**workload-1** in this example).                         |
         +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Protocol                          | Select a protocol. Currently, the TCP and UDP protocols are supported. The ICMP protocol is not supported.                                                     |
         +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Destination Container Port        | Specify a port on which the workload in the container image listens. The Nginx application listens on port 80.                                                 |
         |                                   |                                                                                                                                                                |
         |                                   | If no container port is specified, all ports can be accessed by default.                                                                                       |
         +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Whitelisted Workloads             | Select other workloads that can access the current workload. These workloads will access the current workload at the destination container port.               |
         |                                   |                                                                                                                                                                |
         |                                   | -  **Namespace**: All workloads in the selected namespace(s) are added to the whitelist. That is, all workloads in the namespace(s) can access **workload-1**. |
         |                                   | -  **Workload**: The selected workloads can access **workload-1**. Only other workloads in the same namespace as **workload-1** can be selected.               |
         +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Create**.

#. Repeat the preceding steps to add more network policies for the current workload when other ports need to be accessed by some workloads.

   After the network policies are created, only the specified workloads or workloads in the specified namespaces can access the current workload.
