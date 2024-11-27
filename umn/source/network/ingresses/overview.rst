:original_name: cce_10_0094.html

.. _cce_10_0094:

Overview
========

Why We Need Ingresses
---------------------

A Service is generally used to forward access requests based on TCP and UDP and provide layer-4 load balancing for clusters. However, in actual scenarios, if there is a large number of HTTP/HTTPS access requests on the application layer, the Service cannot meet the forwarding requirements. Therefore, the Kubernetes cluster provides an HTTP-based access mode, ingress.

An ingress is an independent resource in the Kubernetes cluster and defines rules for forwarding external access traffic. As shown in :ref:`Figure 1 <cce_10_0094__fig18155819416>`, you can customize forwarding rules based on domain names and URLs to implement fine-grained distribution of access traffic.

.. _cce_10_0094__fig18155819416:

.. figure:: /_static/images/en-us_image_0000001950317392.png
   :alt: **Figure 1** Ingress diagram

   **Figure 1** Ingress diagram

The following describes the ingress-related definitions:

-  Ingress object: a set of access rules that forward requests to specified Services based on domain names or URLs. It can be added, deleted, modified, and queried by calling APIs.
-  Ingress Controller: an executor for request forwarding. It monitors the changes of resource objects such as ingresses, Services, endpoints, secrets (mainly TLS certificates and keys), nodes, and ConfigMaps in real time, parses rules defined by ingresses, and forwards requests to the target backend Services.

Ingress Controllers provided by different vendors are implemented in different ways. Based on the types of load balancers, Ingress Controllers are classified into LoadBalancer Ingress Controller and Nginx Ingress Controller. Both of them are supported in CCE. LoadBalancer Ingress Controller forwards traffic through ELB. Nginx Ingress Controller uses the templates and images maintained by the Kubernetes community to forward traffic through the Nginx component.

Ingress Feature Comparison
--------------------------

.. table:: **Table 1** Comparison between ingress features

   +-----------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+
   | Feature                                       | ELB Ingress Controller                                                                                                                                           | Nginx Ingress Controller                                                                                     |
   +===============================================+==================================================================================================================================================================+==============================================================================================================+
   | O&M                                           | O&M-free                                                                                                                                                         | Self-installation, upgrade, and maintenance                                                                  |
   +-----------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+
   | Performance                                   | One ingress supports only one load balancer.                                                                                                                     | Multiple ingresses support one load balancer.                                                                |
   +-----------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+
   |                                               | Enterprise-grade load balancers are used to provide high performance and high availability. Service forwarding is not affected in upgrade and failure scenarios. | Performance varies depending on the resource configuration of pods.                                          |
   +-----------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+
   |                                               | Dynamic loading is supported.                                                                                                                                    | -  Processes must be reloaded for non-backend endpoint changes, which causes loss to persistent connections. |
   |                                               |                                                                                                                                                                  | -  Lua supports hot updates of endpoint changes.                                                             |
   |                                               |                                                                                                                                                                  | -  Processes must be reloaded for a Lua modification.                                                        |
   +-----------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+
   | Component deployment                          | Deployed on the master node                                                                                                                                      | Deployed on worker nodes, and operations costs required for the Nginx component                              |
   +-----------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+
   | Route redirection                             | Supported                                                                                                                                                        | Supported                                                                                                    |
   +-----------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+
   | SSL configuration                             | Supported                                                                                                                                                        | Supported                                                                                                    |
   +-----------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+
   | Using ingress as a proxy for backend services | Supported                                                                                                                                                        | Supported, which can be implemented through backend-protocol: HTTPS annotations.                             |
   +-----------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------+

The LoadBalancer ingress is essentially different from the open source Nginx Ingress. Therefore, their supported Service types are different. For details, see :ref:`Services Supported by Ingresses <cce_10_0094__section3565202819276>`.

LoadBalancer Ingress Controller is deployed on a master node. All policies and forwarding behaviors are configured on the ELB side. Load balancers outside the cluster can connect to nodes in the cluster only through the IP address of the VPC in non-passthrough networking scenarios. Therefore, LoadBalancer ingresses support only NodePort Services. However, in the passthrough networking scenario (CCE Turbo cluster + dedicated load balancer), ELB can directly forward traffic to pods in the cluster. In this case, the ingress can only interconnect with ClusterIP Services.

Nginx Ingress Controller runs in a cluster and is exposed as a Service through NodePort. Traffic is forwarded to other Services in the cluster through Nginx-ingress. The traffic forwarding behavior and forwarding object are in the cluster. Therefore, both ClusterIP and NodePort Services are supported.

In conclusion, LoadBalancer ingresses use enterprise-grade load balancers to forward traffic and delivers high performance and stability. Nginx Ingress Controller is deployed on cluster nodes, which consumes cluster resources but has better configurability.

Working Rules of LoadBalancer Ingress Controller
------------------------------------------------

LoadBalancer Ingress Controller developed by CCE implements layer-7 network access for the internet and intranet (in the same VPC) based on ELB and distributes access traffic to the corresponding Services using different URLs.

LoadBalancer Ingress Controller is deployed on the master node and bound to the load balancer in the VPC where the cluster resides. Different domain names, ports, and forwarding policies can be configured for the same load balancer (with the same IP address). :ref:`Figure 2 <cce_10_0094__fig122542486129>` shows the working rules of LoadBalancer Ingress Controller.

#. A user creates an ingress object and configures a traffic access rule in the ingress, including the load balancer, URL, SSL, and backend service port.
#. When Ingress Controller detects that the ingress object changes, it reconfigures the listener and backend server route on the ELB side according to the traffic access rule.
#. When a user accesses a workload, the traffic is forwarded to the corresponding backend service port based on the forwarding policy configured on ELB, and then forwarded to each associated workload through the Service.

.. _cce_10_0094__fig122542486129:

.. figure:: /_static/images/en-us_image_0000001981276941.png
   :alt: **Figure 2** Working rules of shared LoadBalancer ingresses in CCE standard and Turbo clusters

   **Figure 2** Working rules of shared LoadBalancer ingresses in CCE standard and Turbo clusters

When you use **a dedicated load balancer in a CCE Turbo cluster**, pod IP addresses are allocated from the VPC and the load balancer can directly access the pods. When creating an ingress for external cluster access, you can use ELB to access a ClusterIP Service and use pods as the backend server of the ELB listener. In this way, external traffic can directly access the pods in the cluster without being forwarded by node ports.


.. figure:: /_static/images/en-us_image_0000001950317380.png
   :alt: **Figure 3** Working rules of passthrough networking for dedicated LoadBalancer ingresses in CCE Turbo clusters

   **Figure 3** Working rules of passthrough networking for dedicated LoadBalancer ingresses in CCE Turbo clusters

Working Rules of Nginx Ingress Controller
-----------------------------------------

Nginx Ingress uses ELB as the traffic ingress. The :ref:`nginx-ingress <cce_10_0034>` add-on is deployed in a cluster to balance traffic and control access.

.. note::

   nginx-ingress uses the templates and images provided by the open-source community, and issues may occur during usage. CCE periodically synchronizes the community version to fix known vulnerabilities. Check whether your service requirements can be met.

   You can visit the `open source community <https://github.com/kubernetes/ingress-nginx>`__ for more information.

Nginx Ingress Controller is deployed on worker nodes through pods, which will result in O&M costs and Nginx component running overheads. :ref:`Figure 4 <cce_10_0094__fig2042781115133>` shows the working rules of Nginx Ingress Controller.

#. After you update ingress resources, Nginx Ingress Controller writes a forwarding rule defined in the ingress resources into the **nginx.conf** configuration file of Nginx.
#. The built-in Nginx component reloads the updated configuration file to modify and update the Nginx forwarding rule.
#. When traffic accesses a cluster, the traffic is first forwarded by the created load balancer to the Nginx component in the cluster. Then, the Nginx component forwards the traffic to each workload based on the forwarding rule.

.. _cce_10_0094__fig2042781115133:

.. figure:: /_static/images/en-us_image_0000001981276949.png
   :alt: **Figure 4** Working rules of Nginx Ingress Controller

   **Figure 4** Working rules of Nginx Ingress Controller

.. _cce_10_0094__section3565202819276:

Services Supported by Ingresses
-------------------------------

:ref:`Table 2 <cce_10_0094__table143264518141>` lists the Services supported by LoadBalancer ingresses.

.. _cce_10_0094__table143264518141:

.. table:: **Table 2** Services supported by LoadBalancer ingresses

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

:ref:`Table 3 <cce_10_0094__table5498135922712>` lists the Services supported by Nginx Ingress.

.. _cce_10_0094__table5498135922712:

.. table:: **Table 3** Services supported by Nginx Ingress

   ==================== ======================= ========= =========
   Cluster Type         ELB Type                ClusterIP NodePort
   ==================== ======================= ========= =========
   CCE standard cluster Shared load balancer    Supported Supported
   \                    Dedicated load balancer Supported Supported
   CCE Turbo cluster    Shared load balancer    Supported Supported
   \                    Dedicated load balancer Supported Supported
   ==================== ======================= ========= =========
