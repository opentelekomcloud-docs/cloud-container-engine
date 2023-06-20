:original_name: cce_10_0094.html

.. _cce_10_0094:

Ingress Overview
================

Why We Need Ingresses
---------------------

A Service is generally used to forward access requests based on TCP and UDP and provide layer-4 load balancing for clusters. However, in actual scenarios, if there is a large number of HTTP/HTTPS access requests on the application layer, the Service cannot meet the forwarding requirements. Therefore, the Kubernetes cluster provides an HTTP-based access mode, that is, ingress.

An ingress is an independent resource in the Kubernetes cluster and defines rules for forwarding external access traffic. As shown in :ref:`Figure 1 <cce_10_0094__fig18155819416>`, you can customize forwarding rules based on domain names and URLs to implement fine-grained distribution of access traffic.

.. _cce_10_0094__fig18155819416:

.. figure:: /_static/images/en-us_image_0000001517903200.png
   :alt: **Figure 1** Ingress diagram

   **Figure 1** Ingress diagram

The following describes the ingress-related definitions:

-  Ingress object: a set of access rules that forward requests to specified Services based on domain names or URLs. It can be added, deleted, modified, and queried by calling APIs.
-  Ingress Controller: an executor for request forwarding. It monitors the changes of resource objects such as ingresses, Services, endpoints, secrets (mainly TLS certificates and keys), nodes, and ConfigMaps in real time, parses rules defined by ingresses, and forwards requests to the corresponding backend Services.

Working Principle of ELB Ingress Controller
-------------------------------------------

ELB Ingress Controller developed by CCE implements layer-7 network access for the internet and intranet (in the same VPC) based on ELB and distributes access traffic to the corresponding Services using different URLs.

ELB Ingress Controller is deployed on the master node and bound to the load balancer in the VPC where the cluster resides. Different domain names, ports, and forwarding policies can be configured for the same load balancer (with the same IP address). :ref:`Figure 2 <cce_10_0094__fig122542486129>` shows the working principle of ELB Ingress Controller.

#. A user creates an ingress object and configures a traffic access rule in the ingress, including the load balancer, URL, SSL, and backend service port.
#. When Ingress Controller detects that the ingress object changes, it reconfigures the listener and backend server route on the ELB side according to the traffic access rule.
#. When a user accesses a workload, the traffic is forwarded to the corresponding backend service port based on the forwarding policy configured on ELB, and then forwarded to each associated workload through the Service.

.. _cce_10_0094__fig122542486129:

.. figure:: /_static/images/en-us_image_0000001568822925.png
   :alt: **Figure 2** Working principle of ELB Ingress Controller

   **Figure 2** Working principle of ELB Ingress Controller
