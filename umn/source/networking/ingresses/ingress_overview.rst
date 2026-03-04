:original_name: cce_10_0094.html

.. _cce_10_0094:

Ingress Overview
================

Why We Need Ingresses?
----------------------

A Service is generally used to forward access requests based on TCP and UDP and provide layer-4 load balancing for clusters. However, in actual scenarios, if there is a large number of HTTP/HTTPS access requests on the application layer, the Service cannot meet the forwarding requirements. Therefore, the Kubernetes cluster provides an HTTP-based access mode, ingress.

An ingress is an independent resource in the Kubernetes cluster and defines rules for forwarding external access traffic. As shown in :ref:`Figure 1 <cce_10_0094__fig18155819416>`, you can customize forwarding rules based on domain names and URLs to implement fine-grained distribution of access traffic.

.. _cce_10_0094__fig18155819416:

.. figure:: /_static/images/en-us_image_0000002516079671.png
   :alt: **Figure 1** Ingress diagram

   **Figure 1** Ingress diagram


Ingress Overview
----------------

Kubernetes uses ingress resources to define how incoming traffic should be handled, while the Ingress Controller is responsible for processing the actual traffic.

-  **Ingress object**: a set of access rules that forward requests to specified Services based on domain names or paths. It can be added, deleted, modified, and queried by calling APIs.

-  **Ingress Controller**: an executor for forwarding requests. It monitors the changes of resource objects such as ingresses, Services, endpoints, secrets (mainly TLS certificates and keys), nodes, and ConfigMaps in real time, parses rules defined by ingresses, and forwards requests to the target backend Services.

   The way of implementing Ingress Controllers varies depending on their vendors. CCE supports LoadBalancer Ingress Controllers and NGINX Ingress Controllers.

   -  LoadBalancer Ingress Controllers are deployed on master nodes and forward traffic based on the ELB. All policy configurations and forwarding behaviors are managed on the ELB.
   -  NGINX Ingress Controllers are deployed in clusters using charts and images maintained by the Kubernetes community. They provide external access through NodePort and forward external traffic to other services in the cluster through Nginx. All traffic forwarding behaviors and forwarding objects are within the cluster.

Ingress Feature Comparison
--------------------------

.. table:: **Table 1** Comparison between ingress features

   +-----------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------+
   | Feature                                       | LoadBalancer Ingress Controller                                                                                                                                  | NGINX Ingress Controller                                                                                    |
   +===============================================+==================================================================================================================================================================+=============================================================================================================+
   | O&M                                           | O&M-free                                                                                                                                                         | Self-installation, upgrade, and maintenance                                                                 |
   +-----------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------+
   | Performance                                   | One ingress supports only one load balancer.                                                                                                                     | Multiple ingresses support one load balancer.                                                               |
   +-----------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------+
   |                                               | Enterprise-grade load balancers are used to provide high performance and high availability. Service forwarding is not affected in upgrade and failure scenarios. | Performance varies depending on the resource configuration of pods.                                         |
   +-----------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------+
   |                                               | Dynamic loading is supported.                                                                                                                                    | -  Processes must be reloaded for non-backend endpoint changes. This causes loss to persistent connections. |
   |                                               |                                                                                                                                                                  | -  Lua supports hot updates of endpoint changes.                                                            |
   |                                               |                                                                                                                                                                  | -  Processes must be reloaded for a Lua modification.                                                       |
   +-----------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------+
   | Component deployment                          | Deployed on master nodes                                                                                                                                         | Deployed on worker nodes, and operations costs required for the Nginx component                             |
   +-----------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------+
   | Route redirection                             | Supported                                                                                                                                                        | Supported                                                                                                   |
   +-----------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------+
   | SSL configuration                             | Supported                                                                                                                                                        | Supported                                                                                                   |
   +-----------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------+
   | Using ingress as a proxy for backend services | Supported                                                                                                                                                        | Supported, which can be implemented through backend-protocol: HTTPS annotations.                            |
   +-----------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------+

The LoadBalancer ingress is essentially different from the open source Nginx Ingress. Therefore, their supported Service types are different. For details, see :ref:`Services Supported by LoadBalancer Ingresses <cce_10_0094__section3565202819276>`.

LoadBalancer Ingress Controllers are deployed on master nodes. All policy configurations and forwarding behaviors are managed on the ELB. Load balancers outside the cluster can connect to nodes in the cluster only through the IP address of the VPC in non-passthrough networking scenarios. Therefore, LoadBalancer ingresses support only NodePort Services. However, in the passthrough networking scenario where a dedicated load balancer is used in a CCE Turbo cluster, ELB can directly forward traffic to pods in the cluster. In this case, the ingress can only interconnect with ClusterIP Services.

NGINX Ingress Controller runs in a cluster and is exposed as a Service through NodePort. Traffic is forwarded to other Services in the cluster through Nginx ingresses. The traffic forwarding behavior and forwarding object are in the cluster. Therefore, both ClusterIP and NodePort Services are supported.

In conclusion, LoadBalancer ingresses use enterprise-grade load balancers to forward traffic and delivers high performance and stability. NGINX Ingress Controller is deployed on cluster nodes, which consumes cluster resources but has better configurability.

Working Rules of a LoadBalancer Ingress Controller
--------------------------------------------------

CCE LoadBalancer Ingress Controllers provide Layer 7 network access for the internet and intranet (within the same VPC) via ELB, routing traffic to target Services through different paths.

LoadBalancer Ingress Controllers are deployed on master nodes and bound to load balancers in the cluster's VPC. You can configure different domain names, ports, and forwarding policies for the same load balancer (with the same IP address). The working rules of LoadBalancer Ingress Controllers are as follows:

#. A user creates an ingress and configures a traffic access rule in the ingress, including the load balancer, access path, SSL, and backend Service port.
#. When Ingress Controller detects that the ingress changes, it reconfigures the listener and backend server route on the ELB according to the traffic access rule.
#. When a user attempts to access a workload, the ELB forwards the traffic to the target workload according to the configured forwarding rule.

CCE Standard Clusters
---------------------


.. figure:: /_static/images/en-us_image_0000002516079667.png
   :alt: **Figure 2** Working flow of a LoadBalancer ingress in a CCE standard cluster

   **Figure 2** Working flow of a LoadBalancer ingress in a CCE standard cluster

CCE Turbo Clusters Where a Shared Load Balancer Is Used
-------------------------------------------------------


.. figure:: /_static/images/en-us_image_0000002483959718.png
   :alt: **Figure 3** Working flow of a LoadBalancer ingress in a CCE Turbo cluster where a shared load balancer is used

   **Figure 3** Working flow of a LoadBalancer ingress in a CCE Turbo cluster where a shared load balancer is used

CCE Turbo Clusters Where a Dedicated Load Balancer Is Used
----------------------------------------------------------

When a **CCE Turbo cluster** is used, pod IP addresses are directly allocated from the VPC. **Dedicated load balancers** enable passthrough networking to pods. When creating an ingress for external cluster access, you can use ELB to access a ClusterIP Service and use pods as the backend server of the ELB listener. In this way, external traffic can directly access the pods in the cluster without being forwarded by node ports.


.. figure:: /_static/images/en-us_image_0000002483959720.png
   :alt: **Figure 4** Working flow of a LoadBalancer ingress in a CCE Turbo cluster where a dedicated load balancer is used

   **Figure 4** Working flow of a LoadBalancer ingress in a CCE Turbo cluster where a dedicated load balancer is used

Working Rules of NGINX Ingress Controller
-----------------------------------------

Nginx Ingress uses ELB as the traffic ingress. The :ref:`NGINX Ingress Controller <cce_10_0034>` add-on is deployed in a cluster to balance traffic and control access.

.. note::

   NGINX Ingress Controller uses the charts and images provided by the `open-source community <https://github.com/kubernetes/ingress-nginx>`__, and issues may occur during usage. CCE periodically synchronizes the community version to fix known vulnerabilities. Check whether your service requirements can be met.

NGINX Ingress Controller is deployed on worker nodes through pods, which will result in O&M costs and Nginx component running overheads. :ref:`Figure 5 <cce_10_0094__fig2042781115133>` shows the working rules of NGINX Ingress Controller.

#. After you update ingress resources, NGINX Ingress Controller writes a forwarding rule defined in the ingress resources into the **nginx.conf** configuration file of Nginx.
#. The built-in Nginx component reloads the updated configuration file to modify and update the Nginx forwarding rule.
#. When traffic accesses a cluster, the traffic is first forwarded by the created load balancer to the Nginx component in the cluster. Then, the Nginx component forwards the traffic to each workload based on the forwarding rule.

.. _cce_10_0094__fig2042781115133:

.. figure:: /_static/images/en-us_image_0000002483959722.png
   :alt: **Figure 5** Working rules of NGINX Ingress Controller

   **Figure 5** Working rules of NGINX Ingress Controller

.. _cce_10_0094__section3565202819276:

Services Supported by LoadBalancer Ingresses
--------------------------------------------

.. table:: **Table 2** Services supported by LoadBalancer ingresses

   ==================== ======================= ============= =========
   Cluster Type         ELB Type                ClusterIP     NodePort
   ==================== ======================= ============= =========
   CCE standard cluster Shared load balancer    Not supported Supported
   \                    Dedicated load balancer Not supported Supported
   CCE Turbo cluster    Shared load balancer    Not supported Supported
   \                    Dedicated load balancer Supported     Supported
   ==================== ======================= ============= =========

Services Supported by Nginx Ingresses
-------------------------------------

.. table:: **Table 3** Services supported by Nginx ingress

   ==================== ======================= ========= =========
   Cluster Type         ELB Type                ClusterIP NodePort
   ==================== ======================= ========= =========
   CCE standard cluster Shared load balancer    Supported Supported
   \                    Dedicated load balancer Supported Supported
   CCE Turbo cluster    Shared load balancer    Supported Supported
   \                    Dedicated load balancer Supported Supported
   ==================== ======================= ========= =========
