:original_name: cce_10_0954.html

.. _cce_10_0954:

Configuring Multiple Ingresses to Use the Same External ELB Port
================================================================

In a cluster, multiple ingresses can share a listener, allowing them to use the same port on a single load balancer. If two ingresses have different listener configurations, the listener configuration of the earlier ingress (known as the first route) will be used.

The following table lists listener parameters.

+------------------------------------+---------------------------------------+-------------------------------------------------------------------------------------------------+
| Listener                           | Annotation                            | Documentation                                                                                   |
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
