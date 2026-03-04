:original_name: cce_10_0954.html

.. _cce_10_0954:

Configuring Multiple Ingresses in a Cluster to Use the Same Load Balancer
=========================================================================

Multiple Ingresses Accessing the Same External ELB Port
-------------------------------------------------------

In a cluster, you can configure multiple ingresses to use the same ELB listener, allowing them to use the same port on a single load balancer. If two ingresses have different listener configurations, the listener configuration of the earlier ingress (known as the first route) will be used.


.. figure:: /_static/images/en-us_image_0000002516198993.png
   :alt: **Figure 1** Diagram for multiple ingresses to use the same external ELB port

   **Figure 1** Diagram for multiple ingresses to use the same external ELB port

The following table lists listener parameters.

+------------------------------------+---------------------------------------+-------------------------------------------------------------------------------------------------+
| Listener                           | Annotation                            | Helpful Links                                                                                   |
+====================================+=======================================+=================================================================================================+
| Configuring ELB Certificates       | kubernetes.io/elb.tls-certificate-ids | :ref:`Configuring an HTTPS Certificate for a LoadBalancer Ingress <cce_10_0687>`                |
|                                    |                                       |                                                                                                 |
|                                    | kubernetes.io/elb.tls-ciphers-policy  |                                                                                                 |
+------------------------------------+---------------------------------------+-------------------------------------------------------------------------------------------------+
| Using HTTP/2                       | kubernetes.io/elb.http2-enable        | :ref:`Configuring HTTP/2 for a LoadBalancer Ingress <cce_10_0694>`                              |
+------------------------------------+---------------------------------------+-------------------------------------------------------------------------------------------------+
| Configuring Timeout for an Ingress | kubernetes.io/elb.keepalive_timeout   | :ref:`Configuring Timeout for a LoadBalancer Ingress <cce_10_0730>`                             |
|                                    |                                       |                                                                                                 |
|                                    | kubernetes.io/elb.client_timeout      |                                                                                                 |
|                                    |                                       |                                                                                                 |
|                                    | kubernetes.io/elb.member_timeout      |                                                                                                 |
+------------------------------------+---------------------------------------+-------------------------------------------------------------------------------------------------+
| Configuring a Blocklist/Trustlist  | kubernetes.io/elb.acl-id              | :ref:`Configuring a Blocklist/Trustlist Access Policy for a LoadBalancer Ingress <cce_10_0832>` |
|                                    |                                       |                                                                                                 |
|                                    | kubernetes.io/elb.acl-status          |                                                                                                 |
|                                    |                                       |                                                                                                 |
|                                    | kubernetes.io/elb.acl-type            |                                                                                                 |
+------------------------------------+---------------------------------------+-------------------------------------------------------------------------------------------------+

Ensure that the configurations of different listeners for various ingresses are synchronized. To do so, perform the following operations:

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Services & Ingresses**. Then, click the **Ingresses** tab, and choose **More** > **Update** in the **Operation** column.
#. **Synchronize Configuration** is available if the listener configuration of the ingress differs from that of the ELB. Click **Synchronize Configuration**. Then, the listener configuration will be automatically synchronized.

Multiple Ingresses Associated with the Same Service Accessing the Same ELB Port
-------------------------------------------------------------------------------

When you create an ingress, CCE automatically adds a backend server group on the ELB based on the associated Service.

In a cluster, if multiple ingresses access the same ELB port and are associated with the same Service and port, only one backend server group will be created on the ELB. The configuration applied to this backend server group will be that of the first ingress created (referred to as the first route). If ingresses use multiple load balancers to access the same Service, multiple backend server groups will be created. In this case, the sequence of ingress configurations does not matter.


.. figure:: /_static/images/en-us_image_0000002484119016.png
   :alt: **Figure 2** Diagram for multiple ingresses associated with the same Service to access the same ELB port

   **Figure 2** Diagram for multiple ingresses associated with the same Service to access the same ELB port

The following table lists backend server settings.

+------------------------+-----------------------------+--------------------------------------------------------------------------+
| Backend Server Setting | Annotation                  | Helpful Link                                                             |
+========================+=============================+==========================================================================+
| Slow start             | kubernetes.io/elb.slowstart | :ref:`Configuring a Slow Start for a LoadBalancer Ingress <cce_10_0735>` |
+------------------------+-----------------------------+--------------------------------------------------------------------------+
