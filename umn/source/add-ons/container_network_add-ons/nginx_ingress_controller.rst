:original_name: cce_10_0034.html

.. _cce_10_0034:

NGINX Ingress Controller
========================

Introduction
------------

Kubernetes uses kube-proxy to expose Services and provide load balancing. The implementation is at the transport layer. When it comes to Internet applications, where a bucket-load of information is generated, forwarding needs to be more fine-grained, precisely and flexibly controlled by policies and load balancers to deliver higher performance.

This is where ingresses enter. Ingresses provide application-layer forwarding functions, such as virtual hosts, load balancing, SSL proxy, and HTTP routing, for Services that can be directly accessed outside a cluster.

Kubernetes has officially released the Nginx-based Ingress controller. CCE Nginx Ingress controller directly uses community templates and images. The NGINX Ingress Controller generates Nginx configuration and stores the configuration using ConfigMap. The configuration will be written to Nginx pods through the Kubernetes API. In this way, the Nginx configuration is modified and updated. For details, see :ref:`How the Add-on Works <cce_10_0034__section971014351338>`.

You can visit the `open source community <https://github.com/kubernetes/ingress-nginx>`__ for more information.

.. note::

   -  Starting from version 2.3.3, NGINX Ingress Controller only supports TLS v1.2 and v1.3 by default. If the TLS version is earlier than v1.2, an error will occur during the negotiation process between the client and Nginx Ingress. If more versions of TLS are needed, see `TLS/HTTPS <https://kubernetes.github.io/ingress-nginx/user-guide/tls/#default-tls-version-and-ciphers>`__.
   -  When installing the NGINX Ingress Controller, you can specify Nginx parameters. These parameters take effect globally and are contained in the **nginx.conf** file. You can search for the parameters in `ConfigMaps <https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/>`__. If the parameters are not included in ConfigMaps, the configurations will not take effect.
   -  Do not manually modify or delete the load balancer and listener that are automatically created by CCE. Otherwise, the workload will be abnormal. If you have modified or deleted them by mistake, uninstall the nginx-ingress add-on and re-install it.

.. _cce_10_0034__section971014351338:

How the Add-on Works
--------------------

NGINX Ingress Controller consists of the ingress object, ingress controller, and Nginx. The ingress controller assembles ingresses into the Nginx configuration file (nginx.conf) and reloads Nginx to make the changed configurations take effect. When it detects that the pod in a Service changes, it dynamically changes the upstream server group configuration of Nginx. In this case, the Nginx process does not need to be reloaded. :ref:`Figure 1 <cce_10_0034__fig204075132570>` shows how this add-on works.

-  An ingress is a group of access rules that forward requests to specified Services based on domain names or URLs. Ingresses are stored in the object storage service etcd and are added, deleted, modified, and queried through APIs.
-  The ingress controller monitors the changes of resource objects such as ingresses, Services, endpoints, secrets (mainly TLS certificates and keys), nodes, and ConfigMaps in real time and automatically performs operations on Nginx.
-  Nginx implements load balancing and access control at the application layer.

.. _cce_10_0034__fig204075132570:

.. figure:: /_static/images/en-us_image_0000001981436501.png
   :alt: **Figure 1** Working principles of NGINX Ingress Controller

   **Figure 1** Working principles of NGINX Ingress Controller

Precautions
-----------

-  For clusters earlier than v1.23, **kubernetes.io/ingress.class: "nginx"** must be added to the annotation of the ingress created through the API.
-  Dedicated load balancers must be the network type (TCP/UDP) supporting private networks (with a private IP).
-  If the node where NGINX Ingress Controller runs and containers on this node cannot access Nginx ingress, you need to configure anti-affinity for the workload pods and Nginx Ingress Controller. For details, see :ref:`Configuring Anti-affinity Between a Workload and Nginx Ingress Controller <cce_10_0034__section15456140194414>`.
-  During the NGINX Ingress Controller pod upgrade, 10s are reserved for deleting the NGINX Ingress Controller at the ELB backend.
-  The timeout duration for the graceful exit of the NGINX Ingress Controller is 300s. If the timeout duration is longer than 300s during the upgrade of the NGINX Ingress Controller, persistent connections will be disconnected, and connectivity will be interrupted for a short period of time.

Prerequisites
-------------

Before installing this add-on, you have one available cluster and there is a node running properly. If no cluster is available, create one according to :ref:`Creating a CCE Standard/Turbo Cluster <cce_10_0028>`.

.. _cce_10_0034__section1152424015224:

Installing the Add-on
---------------------

#. Log in to the CCE console and click the cluster name to access the cluster console. Choose **Add-ons** in the navigation pane, locate **NGINX Ingress Controller** on the right, and click **Install**.
#. On the **Install Add-on** page, configure the specifications.

   .. table:: **Table 1** Add-on configuration

      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                               |
      +===================================+===========================================================================================================================================+
      | Add-on Specifications             | Nginx Ingress can be deployed based on customized resource specifications.                                                                |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+
      | Pods                              | You can adjust the number of add-on instances as required.                                                                                |
      |                                   |                                                                                                                                           |
      |                                   | High availability is not possible with a single pod. If an error occurs on the node where the add-on instance runs, the add-on will fail. |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+
      | Containers                        | You can adjust the container specifications of an add-on instance as required.                                                            |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+

#. Configure the add-on parameters.

   -  .. _cce_10_0034__li0953175016455:

      **Ingress Class**: Enter a custom controller name, which uniquely identifies an Ingress controller. The name of each controller in the same cluster must be unique and cannot be set to **cce**. (**cce** is the unique identifier of the ELB Ingress Controller.) When creating an Ingress, you can specify the controller name to declare which controller should manage this Ingress.

   -  **Namespace**: Select a namespace where the ingress controller is in.

   -  **Load Balancer**: Select a shared or dedicated load balancer. If no load balancer is available, create one. The load balancer has at least two listeners, and ports 80 and 443 are not occupied by listeners.

   -  **Admission Check**: Admission control is performed on Ingresses to ensure that the controller can generate valid configurations. Admission verification is performed on the configuration of Nginx Ingresses. If the verification fails, the request will be intercepted. For details about admission verification, see `Access Control <https://kubernetes.github.io/ingress-nginx/e2e-tests/#admission-admission-controller>`__.

      .. note::

         -  Admission check slows down the responses to Ingress requests.
         -  Only add-ons of version 2.4.1 or later support admission verification.

   -  **Nginx Parameters**: Configuring the **nginx.conf** file will affect all managed ingresses. You can search for related parameters through `ConfigMaps <https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/>`__. If the parameters you configured are not included in the options listed in the `ConfigMaps <https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap/>`__, the parameters will not take effect.

      For example, you can use the **keep-alive-requests** parameter to describe how to set the maximum number of requests for keeping active connections to 100.

      .. code-block::

         {
             "keep-alive-requests": "100"
         }

   -  **Enabling Indicator Collection**: If the add-on version is 2.4.12 or later, Prometheus monitoring metrics can be collected.

   -  **Default server certificate**: Select an IngressTLS or kubernetes.io/tls key to configure the default certificate when an Nginx Ingress Controller is started. If no secret is available, click **Create TLS Secret**. For details, see :ref:`Creating a Secret <cce_10_0153>`. For details about the default server certificate, see `Default SSL Certificate <https://kubernetes.github.io/ingress-nginx/user-guide/tls/#default-ssl-certificate>`__.

   -  **404 Service**: By default, the 404 service provided by the add-on is used. To customize the 404 service, enter the namespace/service name. If the service does not exist, the add-on installation will fail.

   -  **Adding a TCP/UDP Service**: By default, Nginx Ingress Controller can forward only external HTTP and HTTPS traffic. You can add TCP/UDP port mapping to forward external TCP/UDP traffic to services in the cluster. For more information about adding TCP/UDP services, see `Exposing TCP and UDP services <https://kubernetes.github.io/ingress-nginx/user-guide/exposing-tcp-udp-services/>`__.

      -  **Protocol**: Select **TCP** or **UDP**.
      -  **Service Port**: specifies the port used by the ELB listener. The port number ranges from 1 to 65535.
      -  **Target Service Namespace**: Select the namespace where the Service is in.
      -  **Destination Service**: Select an existing Service. Any services that do not match the search criteria will be filtered out automatically.
      -  **Destination Service Port**: Select the access port of the destination Service.

      .. note::

         -  If the cluster version is v1.19.16-r5, v1.21.8-r0, v1.23.6-r0, or later, the TCP/UDP hybrid protocols can be configured.
         -  If the cluster version is v1.19.16-r5, v1.21.8-r0, v1.23.6-r0, v1.25.2-r0, or later, you can configure the TCP/UDP hybrid protocols to use the same external port.

#. Configure scheduling policies for the add-on.

   .. note::

      -  Scheduling policies do not take effect on add-on instances of the DaemonSet type.
      -  When configuring multi-AZ deployment or node affinity, ensure that there are nodes meeting the scheduling policy and that resources are sufficient in the cluster. Otherwise, the add-on cannot run.

   .. table:: **Table 2** Configurations for add-on scheduling

      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                    |
      +===================================+================================================================================================================================================================================================================================================================================================================================================================================================================================================+
      | Multi AZ                          | -  **Preferred**: Deployment pods of the add-on will be preferentially scheduled to nodes in different AZs. If all the nodes in the cluster are deployed in the same AZ, the pods will be scheduled to that AZ.                                                                                                                                                                                                                                |
      |                                   | -  **Equivalent mode**: Deployment pods of the add-on are evenly scheduled to the nodes in the cluster in each AZ. If a new AZ is added, you are advised to increase add-on pods for cross-AZ HA deployment. With the Equivalent multi-AZ deployment, the difference between the number of add-on pods in different AZs will be less than or equal to 1. If resources in one of the AZs are insufficient, pods cannot be scheduled to that AZ. |
      |                                   | -  **Required**: Deployment pods of the add-on will be forcibly scheduled to nodes in different AZs. If there are fewer AZs than pods, the extra pods will fail to run.                                                                                                                                                                                                                                                                        |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Node Affinity                     | -  **Not configured**: Node affinity is disabled for the add-on.                                                                                                                                                                                                                                                                                                                                                                               |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | -  **Node Affinity**: Specify the nodes where the add-on is deployed. If you do not specify the nodes, the add-on will be randomly scheduled based on the default cluster scheduling policy.                                                                                                                                                                                                                                                   |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | -  **Specified Node Pool Scheduling**: Specify the node pool where the add-on is deployed. If you do not specify the node pool, the add-on will be randomly scheduled based on the default cluster scheduling policy.                                                                                                                                                                                                                          |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | -  **Custom Policies**: Enter the labels of the nodes where the add-on is to be deployed for more flexible scheduling policies. If you do not specify node labels, the add-on will be randomly scheduled based on the default cluster scheduling policy.                                                                                                                                                                                       |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   |    If multiple custom affinity policies are configured, ensure that there are nodes that meet all the affinity policies in the cluster. Otherwise, the add-on cannot run.                                                                                                                                                                                                                                                                      |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Toleration                        | Using both taints and tolerations allows (not forcibly) the add-on Deployment to be scheduled to a node with the matching taints, and controls the Deployment eviction policies after the node where the Deployment is located is tainted.                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | The add-on adds the default tolerance policy for the **node.kubernetes.io/not-ready** and **node.kubernetes.io/unreachable** taints, respectively. The tolerance time window is 60s.                                                                                                                                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   | For details, see :ref:`Configuring Tolerance Policies <cce_10_0728>`.                                                                                                                                                                                                                                                                                                                                                                          |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Install**.

.. _cce_10_0034__section1057051834311:

Installing Multiple Nginx Ingress Controllers
---------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Add-ons**, locate the installed Nginx Ingress Controller, and click **New**.
#. On the page displayed, reconfigure the add-on parameters. For details, see :ref:`Installing the Add-on <cce_10_0034__section1152424015224>`.
#. Click **Install**.
#. Wait until the installation instruction is delivered. Go back to Add-ons, click **Manage**, and view the installed add-on instance on the add-on details page.

Components
----------

.. table:: **Table 3** Add-on components

   +-----------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------+-----------------------+
   | Component                                                                                           | Description                                                                           | Resource Type         |
   +=====================================================================================================+=======================================================================================+=======================+
   | cceaddon-nginx-ingress-<Controller name>-controller                                                 | Nginx Ingress controller, which provides flexible routing and forwarding for clusters | Deployment            |
   |                                                                                                     |                                                                                       |                       |
   | (The controller name in versions earlier than 2.5.4 is **cceaddon-nginx-ingress-controller**.)      |                                                                                       |                       |
   +-----------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------+-----------------------+
   | cceaddon-nginx-ingress-<Controller name>-backend                                                    | Default backend of Nginx Ingress. The message "default backend - 404" is returned.    | Deployment            |
   |                                                                                                     |                                                                                       |                       |
   | (The controller name in versions earlier than 2.5.4 is **cceaddon-nginx-ingress-default-backend**.) |                                                                                       |                       |
   +-----------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------+-----------------------+

.. _cce_10_0034__section15456140194414:

Configuring Anti-affinity Between a Workload and Nginx Ingress Controller
-------------------------------------------------------------------------

To avoid a situation where the node running NGINX Ingress Controller and its containers cannot access the Nginx Ingress Controller, you should set up anti-affinity between the workload and Nginx Ingress Controller. This means that the workload pods cannot be scheduled to the same node as the Nginx Ingress Controller.

.. code-block::

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: nginx
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: nginx
     strategy:
       type: RollingUpdate
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
         - image: nginx:aplpine
           imagePullPolicy: IfNotPresent
           name: nginx
         imagePullSecrets:
         - name: default-secret
         affinity:
           podAntiAffinity:
             requiredDuringSchedulingIgnoredDuringExecution:
               - labelSelector:
                   matchExpressions:    # Implement anti-affinity through the label of the NGINX Ingress Controller pods.
                     - key: app
                       operator: In
                       values:
                         - nginx-ingress   #If multiple NGINX Ingress Controllers are installed in the cluster, the label value is nginx-ingress-<Controller name>.
                     - key: component
                       operator: In
                       values:
                         - controller
                 namespaces:
                   - kube-system
                 topologyKey: kubernetes.io/hostname

Change History
--------------

.. table:: **Table 4** Release history for NGINX Ingress Controller 2.6.x

   +-----------------+---------------------------+-----------------------------------------------------------+----------------------------------------------------------------------------------------+
   | Add-on Version  | Supported Cluster Version | New Feature                                               | Community Version                                                                      |
   +=================+===========================+===========================================================+========================================================================================+
   | 2.6.5           | v1.25                     | Metric collection can be disabled in the startup command. | `1.9.6 <https://github.com/kubernetes/ingress-nginx/releases/tag/controller-v1.9.6>`__ |
   |                 |                           |                                                           |                                                                                        |
   |                 | v1.27                     |                                                           |                                                                                        |
   |                 |                           |                                                           |                                                                                        |
   |                 | v1.28                     |                                                           |                                                                                        |
   |                 |                           |                                                           |                                                                                        |
   |                 | v1.29                     |                                                           |                                                                                        |
   +-----------------+---------------------------+-----------------------------------------------------------+----------------------------------------------------------------------------------------+
