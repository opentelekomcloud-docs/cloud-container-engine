:original_name: cce_10_0094.html

.. _cce_10_0094:

Overview
========

Why We Need Ingresses
---------------------

A Service is generally used to forward access requests based on TCP and UDP and provide layer-4 load balancing for clusters. However, in actual scenarios, if there is a large number of HTTP/HTTPS access requests on the application layer, the Service cannot meet the forwarding requirements. Therefore, the Kubernetes cluster provides an HTTP-based access mode, ingress.

An ingress is an independent resource in the Kubernetes cluster and defines rules for forwarding external access traffic. As shown in :ref:`Figure 1 <cce_10_0094__fig18155819416>`, you can customize forwarding rules based on domain names and URLs to implement fine-grained distribution of access traffic.

.. _cce_10_0094__fig18155819416:

.. figure:: /_static/images/en-us_image_0000001851587340.png
   :alt: **Figure 1** Ingress diagram

   **Figure 1** Ingress diagram

The following describes the ingress-related definitions:

-  Ingress object: a set of access rules that forward requests to specified Services based on domain names or URLs. It can be added, deleted, modified, and queried by calling APIs.
-  Ingress Controller: an executor for request forwarding. It monitors the changes of resource objects such as ingresses, Services, endpoints, secrets (mainly TLS certificates and keys), nodes, and ConfigMaps in real time, parses rules defined by ingresses, and forwards requests to the target backend Services.

Working Rules of LoadBalancer Ingress Controller
------------------------------------------------

LoadBalancer Ingress Controller developed by CCE implements layer-7 network access for the internet and intranet (in the same VPC) based on ELB and distributes access traffic to the corresponding Services using different URLs.

LoadBalancer Ingress Controller is deployed on the master node and bound to the load balancer in the VPC where the cluster resides. Different domain names, ports, and forwarding policies can be configured for the same load balancer (with the same IP address). :ref:`Figure 2 <cce_10_0094__fig122542486129>` shows the working rules of LoadBalancer Ingress Controller.

#. A user creates an ingress object and configures a traffic access rule in the ingress, including the load balancer, URL, SSL, and backend service port.
#. When Ingress Controller detects that the ingress object changes, it reconfigures the listener and backend server route on the ELB side according to the traffic access rule.
#. When a user accesses a workload, the traffic is forwarded to the corresponding backend service port based on the forwarding policy configured on ELB, and then forwarded to each associated workload through the Service.

.. _cce_10_0094__fig122542486129:

.. figure:: /_static/images/en-us_image_0000001851587344.png
   :alt: **Figure 2** Working rules of shared LoadBalancer ingresses in CCE standard and Turbo clusters

   **Figure 2** Working rules of shared LoadBalancer ingresses in CCE standard and Turbo clusters

When you use **a dedicated load balancer in a CCE Turbo cluster**, pod IP addresses are allocated from the VPC and the load balancer can directly access the pods. When creating an ingress for external cluster access, you can use ELB to access a ClusterIP Service and use pods as the backend server of the ELB listener. In this way, external traffic can directly access the pods in the cluster without being forwarded by node ports.


.. figure:: /_static/images/en-us_image_0000001897906717.png
   :alt: **Figure 3** Working rules of passthrough networking for dedicated LoadBalancer ingresses in CCE Turbo clusters

   **Figure 3** Working rules of passthrough networking for dedicated LoadBalancer ingresses in CCE Turbo clusters

.. _cce_10_0094__section3565202819276:

Services Supported by Ingresses
-------------------------------

:ref:`Table 1 <cce_10_0094__table143264518141>` lists the services supported by LoadBalancer ingresses.

.. _cce_10_0094__table143264518141:

.. table:: **Table 1** Services supported by LoadBalancer ingresses

   +----------------------+-------------------------+---------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
   | Cluster Type         | ELB Type                | ClusterIP                                                                                                                             | NodePort                                                                                                                                   |
   +======================+=========================+=======================================================================================================================================+============================================================================================================================================+
   | CCE standard cluster | Shared load balancer    | Not supported                                                                                                                         | Supported                                                                                                                                  |
   +----------------------+-------------------------+---------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
   |                      | Dedicated load balancer | Not supported (Failed to access the dedicated load balancers because no ENI is bound to the associated pod of the ClusterIP Service.) | Supported                                                                                                                                  |
   +----------------------+-------------------------+---------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
   | CCE Turbo cluster    | Shared load balancer    | Not supported                                                                                                                         | Supported                                                                                                                                  |
   +----------------------+-------------------------+---------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
   |                      | Dedicated load balancer | Supported                                                                                                                             | Not supported (Failed to access the dedicated load balancers because an ENI has been bound to the associated pod of the NodePort Service.) |
   +----------------------+-------------------------+---------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+
