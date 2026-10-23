:original_name: cce_bestpractice_10017.html

.. _cce_bestpractice_10017:

Recommended Configurations for HA CCE Clusters
==============================================

This section describes the recommended configurations for a Kubernetes cluster in which applications can run stably and reliably.

+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------+
| Item                  | Description                                                                                                                                                                                                                                                                                                                                          | Recommended Operations                                                                                        |
+=======================+======================================================================================================================================================================================================================================================================================================================================================+===============================================================================================================+
| Cluster control plane | CCE is a hosted Kubernetes cluster service. You do not need to perform O&M on the master nodes. You can configure your cluster specifications to improve the stability and reliability.                                                                                                                                                              | -  :ref:`Deploying Master Nodes in Different AZs <cce_bestpractice_10017__section1655395821216>`              |
|                       |                                                                                                                                                                                                                                                                                                                                                      | -  :ref:`Selecting a Network <cce_bestpractice_10017__section11239640141213>`                                 |
|                       |                                                                                                                                                                                                                                                                                                                                                      | -  :ref:`Configuring Service Forwarding <cce_bestpractice_10017__section386335612345>`                        |
|                       |                                                                                                                                                                                                                                                                                                                                                      | -  :ref:`Configuring Quotas and Limits <cce_bestpractice_10017__section1129916578355>`                        |
+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------+
| Cluster data plane    | In a Kubernetes cluster, the data plane consists of worker nodes that can run containerized applications and transmit network traffic. When using CCE, perform O&M on worker nodes by yourself. To achieve HA, ensure the worker nodes' scalability and repairability and pay attention to the running statuses of the worker nodes' key components. | -  :ref:`Partitioning Data Disks Attached to a Node <cce_bestpractice_10017__section343391551214>`            |
|                       |                                                                                                                                                                                                                                                                                                                                                      | -  :ref:`Running CCE Node Problem Detector <cce_bestpractice_10017__section187571360473>`                     |
|                       |                                                                                                                                                                                                                                                                                                                                                      | -  :ref:`Configuring DNS Caches <cce_bestpractice_10017__section71561443164211>`                              |
|                       |                                                                                                                                                                                                                                                                                                                                                      | -  :ref:`Properly Deploying CoreDNS <cce_bestpractice_10017__section1946610157435>`                           |
+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------+
| Application           | If you want your applications to be always available, especially during peak hours, run them in a scalable and elastic manner and pay attention to their running statuses.                                                                                                                                                                           | -  :ref:`Configuring Multiple Pods for a Single Application <cce_bestpractice_10017__section1014753163714>`   |
|                       |                                                                                                                                                                                                                                                                                                                                                      | -  :ref:`Configuring Resource Quotas for a Workload <cce_bestpractice_10017__section1328232471219>`           |
|                       |                                                                                                                                                                                                                                                                                                                                                      | -  :ref:`Deploying an Application in Multiple AZs <cce_bestpractice_10017__section4339184011374>`             |
|                       |                                                                                                                                                                                                                                                                                                                                                      | -  :ref:`Deploying a System Add-on in Multiple AZs <cce_bestpractice_10017__section17782181564512>`           |
|                       |                                                                                                                                                                                                                                                                                                                                                      | -  :ref:`Configuring Auto Scaling <cce_bestpractice_10017__section20910131333816>`                            |
|                       |                                                                                                                                                                                                                                                                                                                                                      | -  :ref:`Configuring Logs, Monitoring Metrics, and Alarm Rules <cce_bestpractice_10017__section192371129144>` |
+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------+

Cluster Control Plane
---------------------

.. _cce_bestpractice_10017__section1655395821216:

Deploying Master Nodes in Different AZs
---------------------------------------

Multiple regions are provided for you to deploy your services, and there are different availability zones (AZs) in each region. An AZ is a collection of one or more physical data centers with independent cooling, fire extinguishing, moisture-proof, and electricity facilities. AZs within a region are connected using high-speed optical fibers. This allows you to build cross-AZ HA systems.

When creating a cluster, enable the HA mode of the cluster and configure the distribution mode of the master nodes. The master nodes are deployed in different AZs as randomly as possible. This ensures a higher disaster recovery (DR) capability of the cluster.

You can also customize the distribution mode. The following two modes are supported:

-  **Random**: Master nodes are deployed in different AZs for DR.
-  **Custom**: Master nodes are deployed in specific AZs.

   -  **Host**: Master nodes are deployed on different hosts in the same AZ.
   -  **Custom**: Master nodes are deployed in the AZ you specify.

.. _cce_bestpractice_10017__section11239640141213:

Selecting a Network
-------------------

-  Types of networks: CCE supports VPC, Cloud Native 2.0, and container tunnel networks. Different types of networks have different performance and functions. For details, see :ref:`Cluster Network <cce_bestpractice_10016__section13189203510317>`.
-  VPC: To enable your applications to access other cloud services like RDS, create related services in the same VPC as your cluster. This is because services using different VPCs are isolated from each other. If you have VPCs, use VPC peering connections to enable communication between VPCs.
-  Pod CIDR block: Do not configure a small pod CIDR block. Otherwise, the number of supported nodes will be limited.

   -  For a cluster using a VPC network, if the subnet mask of the pod CIDR block is /16, there are 256 x 256 IP addresses available. If the maximum number of pods reserved on each node is 128, the maximum number of nodes supported is 512.
   -  For a cluster using a container tunnel network, if the subnet mask of the pod CIDR block is /16, there are 256 x 256 IP addresses available. The pod CIDR block allocates 16 IP addresses to nodes at a time by default. The maximum number of nodes supported by your cluster is 4,096 (65536/16=4096).
   -  For a cluster using a Cloud Native 2.0 network, the pod CIDR block is a VPC subnet, and the number of containers that can be created depends on the size of the selected subnet.

-  Service CIDR block: The Service CIDR block determines the maximum number of Services in a cluster. Evaluate your actual needs before adjusting this CIDR block. A Service CIDR block cannot be modified after its creation. Do not configure an excessively small one.

For details, see :ref:`Planning CIDR Blocks for a Cluster <cce_bestpractice_00004>`.

.. _cce_bestpractice_10017__section386335612345:

Configuring Service Forwarding
------------------------------

kube-proxy is a key component in a Kubernetes cluster. It is responsible for load balancing and forwarding between a Service and its backend pods. When using clusters, consider the potential performance issues of the forwarding mode.

CCE supports multiple service forwarding modes, each with its own advantages and disadvantages.

-  nftables is a high-performance forwarding mode that eliminates iptables' performance bottlenecks and scalability limitations at scale. It provides a unified configuration framework for multi-protocol rule management. This option is only available in clusters v1.35 and later.
-  IPVS allows higher throughput and faster forwarding. It applies to scenarios involving large clusters or a large number of Services.
-  iptables is the traditional kube-proxy mode. It applies to scenario involving a small number of Services or a large number of short concurrent connections on clients. If there are more than 1,000 Services in a cluster, network delay may occur.

.. _cce_bestpractice_10017__section1129916578355:

Configuring Quotas and Limits
-----------------------------

CCE allows you to configure quotas and limits for your cloud service resources and resources in your clusters. This prevents excessive use of resources. When deploying applications in CCE clusters, consider these limits and periodically review them. This will avoid scaling failures caused by insufficient quotas during application running.

-  Configuring resource quotas for cloud services: Cloud services like ECS, EVS, VPC, ELB, and SWR are also used in CCE clusters. If the existing resource quotas cannot meet your requirements, submit a service ticket and increase the quotas.
-  Configuring resource quotas for a cluster: You are allowed to configure the namespace-level resource quotas to limit the number of objects of a certain type in a namespace and the total computing resources like CPUs and memory consumed by the objects. For details, see :ref:`Configuring Resource Quotas <cce_10_0287>`.

Cluster Data Plane
------------------

.. _cce_bestpractice_10017__section343391551214:

Partitioning Data Disks Attached to a Node
------------------------------------------

A node's default data disk stores the container runtime and kubelet components. For details, see :ref:`Space Allocation of a Data Disk <cce_10_0341>`.

The default space of this data disk is 100 GiB. You can adjust the space as required. Images, system logs, and application logs are stored on data disks. Therefore, you need to evaluate the number of pods to be deployed on each node, the size of logs, images, and temporary data of each pod, as well as some reserved space for the system. For details, see :ref:`Selecting a Data Disk for a Node <cce_bestpractice_10012>`.

.. _cce_bestpractice_10017__section187571360473:

Running CCE Node Problem Detector
---------------------------------

A failure in a worker node may affect the availability of applications. :ref:`CCE Node Problem Detector <cce_10_0132>` is used to monitor node exceptions. It helps you detect and handle latent exceptions in a timely manner. You can also customize the check items, including target nodes, check period, and triggering threshold. For details, see :ref:`Configuring Node Fault Detection Policies <cce_10_0659>`.

.. _cce_bestpractice_10017__section71561443164211:

Configuring DNS Caches
----------------------

When the number of DNS requests in a cluster increases, the load of CoreDNS increases and the following issues may occur:

-  Increased delay: CoreDNS needs to process more requests, which may slow down the DNS query and affect service performance.
-  Increased resource usage: To ensure DNS performance, CoreDNS requires higher specifications.

.. _cce_bestpractice_10017__section1946610157435:

Properly Deploying CoreDNS
--------------------------

Deploy the CoreDNS instances across different AZs and nodes to reduce the impact of a single-node or single-AZ failure.

Ensure that the CPUs and memory of the node where CoreDNS is running are not fully used. Otherwise, the QPS and response of domain name resolution will be affected.

Application
-----------

.. _cce_bestpractice_10017__section1014753163714:

Configuring Multiple Pods for a Single Application
--------------------------------------------------

If your application runs in one pod, the application will be unavailable if the pod is abnormal. Use Deployments or other types of ReplicaSets to deploy your applications. Each time a pod fails or is terminated, the controller automatically restarts a new pod that has the same specifications as the original one. This ensures that a specified number of pods are always running in the cluster.

When creating a workload, set the number of pods to a value greater than 2. If one of the pods is faulty, the remaining pods still run until Kubernetes automatically creates another pod to compensate for the loss. You can also use HPAs and CAs (:ref:`Using an HPA and a CA for Auto Scaling of Workloads and Nodes <cce_bestpractice_00282>`) to automatically scale in or out the workloads as required.

Using Containers to Isolate Processes
-------------------------------------

Containers provide process-level isolation. Each container has its own file system, network, and resources. This prevents interference between different processes and avoids attacks and data leakage from malicious processes. Using containers to isolate processes can improve the reliability, security, and portability of applications.

If several processes work together, you can create multiple containers in a pod so that they can share the same network, PV, and other resources. Taking an init container as an example. An init container runs before the main container is started to complete some initialization tasks like configuring environment variables, loading databases or data, and pulling Git repositories.

Multiple containers in a pod share the lifecycle of this pod. So, if one of the containers is abnormal, the entire pod will be restarted.

.. _cce_bestpractice_10017__section1328232471219:

Configuring Resource Quotas for a Workload
------------------------------------------

Configure and adjust resource requests and limits for all workloads.

If too many pods are scheduled to one node, the node will be overloaded and unable to provide services.

To avoid this problem, when deploying a pod, specify the resource request and limit required by the pod. Kubernetes then selects a node with enough idle resources for this pod. For example, the resource request for an Nginx pod is 1 CPU core and 1,024-MiB memory, and the resource limit for it is 2 CPU cores and 4,096-MiB memory.

Kubernetes statically schedules resources. The remaining resources on each node are calculated as follows: Remaining resources on a node = Total resources on the node - Allocated resources (resources not in use). If you manually run a resource-consuming process, Kubernetes cannot detect it.

Additionally, you must configure **resources** for all pods. For a pod that does not have **resources** configured, after it is scheduled to a node, Kubernetes does not deduct the resources used by this pod from the node on which it runs. Other pods may still be scheduled to this node.

.. _cce_bestpractice_10017__section4339184011374:

Deploying an Application in Multiple AZs
----------------------------------------

You can run pods on nodes in multiple AZs to prevent an application from being affected by faults of a single AZ.

When creating a node, manually specify an AZ for the node.

During application deployment, configure anti-affinity rules for pods so that the scheduler can schedule the pods across multiple AZs. For details, see :ref:`High-Availability Deployment of Applications in CCE <cce_bestpractice_00220>`. The following is an example:

.. code-block::

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: web-server
     labels:
       app: web-server
   spec:
     replicas: 4
     selector:
       matchLabels:
         app: web-server
     template:
       metadata:
         labels:
           app: web-server
       spec:
         containers:
         - name: web-app
           image: nginx
         imagePullSecrets:
         - name: default-secret
         affinity:
           podAntiAffinity:  # Workload anti-affinity
             preferredDuringSchedulingIgnoredDuringExecution:  # The rules should be followed whenever possible; otherwise, pods may fail to schedule when the number of pods exceeds the number of AZs.
             - podAffinityTerm:
                 labelSelector:  # Pod label matching rule. Configure anti-affinity rules between pods and their labels.
                   matchExpressions:
                   - key: app
                     operator: In
                     values:
                     - web-server
                 topologyKey: topology.kubernetes.io/zone  # Topology domain of the AZ where the node is located
               weight: 100

You can also use `Pod Topology Spread Constraints <https://kubernetes.io/docs/concepts/scheduling-eviction/topology-spread-constraints/>`__ to deploy pods in multiple AZs.

.. _cce_bestpractice_10017__section17782181564512:

Deploying a System Add-on in Multiple AZs
-----------------------------------------

The Deployment pods of CCE system add-ons like CoreDNS and CCE Container Storage (Everest) can be deployed in multiple AZs, the same way as deploying an application. This function can satisfy different requirements.

.. table:: **Table 1** Deployment description

   +-----------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Mode      | Configuration Description                                                                                                                                                                                                                            | Usage Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Recommended Configuration Scenario                                                                                                                   |
   +===========+======================================================================================================================================================================================================================================================+==============================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================+======================================================================================================================================================+
   | Preferred | Add-on pods will have the **topology.kubernetes.io/zone** topology key for soft anti-affinity deployment, and the anti-affinity rule is **preferredDuringSchedulingIgnoredDuringExecution**.                                                         | Add-on pods will be preferentially scheduled to nodes in different AZs. If resources in some AZs are insufficient, some add-on pods may be scheduled to the same AZ which has enough resources.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | Scenarios where there are no mandatory requirements for multi-AZ DR                                                                                  |
   +-----------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Forcible  | Add-on pods will have the **topology.kubernetes.io/zone** topology key for hard anti-affinity deployment, and the anti-affinity rule is **requiredDuringSchedulingIgnoredDuringExecution**.                                                          | A maximum of one pod of the same add-on can be deployed in each AZ. The number of running pods cannot exceed the number of AZs in the cluster. If the node where an add-on pod runs is faulty, the pod cannot be automatically migrated to other nodes in the same AZ.                                                                                                                                                                                                                                                                                                                                                                                                                                       | Scenarios where the number of AZs will change (This mode is used to prevent all pods from being scheduled to the node in the current AZ in advance.) |
   +-----------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Balanced  | Add-on pods will have the **topology.kubernetes.io/zone** topology key for configuring topology spread constraints. The pod difference between different topology domains cannot exceed 1 for add-on pods to be evenly distributed in different AZs. | The effect of this mode is between that of the preferred mode and that of the forcible mode. In the balanced mode, add-on pods can be deployed in different AZs. They can also run in the same AZ when there are more pods than AZs. To use this mode, plan node resources in each AZ in advance to ensure that each AZ has enough node resources for pods. This ensures successful deployment of add-on pods and smooth scheduling of these pods during updates although node resources in some AZs are insufficient. If there is more than one add-on pod in a single AZ, the number of nodes available for scheduling those add-on pods in that AZ must exceed the number of add-on pods by at least one. | Scenarios where there are high requirements for DR                                                                                                   |
   +-----------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------+

Configuring a Container Health Check
------------------------------------

Kubernetes automatically restarts pods that are not running properly. This prevents service interruption caused by pod exceptions. In some cases, however, even if a pod is running, it does not mean that it can provide services properly. For example, a deadlock may occur in a process in a running pod, but Kubernetes does not automatically restart the pod because it is still running. To solve this problem, configure a liveness probe to check whether the pod is healthy. If the liveness probe detects a problem, Kubernetes will restart the pod.

You can also configure a readiness probe to check whether a pod can provide services properly. During application startup, the application cannot provide services to external systems because it may take some time for it to initialize. After you configure a readiness probe for the application pod, Kubernetes allows the Service to forward requests to this pod only after the readiness probe detects that the pod is ready. When the pod is faulty, the readiness probe can prevent new traffic from being forwarded to it.

A startup probe checks whether a container has been started. It ensures that the container can start successfully before the liveness and readiness probes do their tasks, and the startup is not affected by the liveness and readiness probes. Configuring startup probes ensures that the startup of slow-start containers can be detected thoroughly to prevent Kubernetes from terminating them before they are started.

You can configure these probes for an application during its deployment. The following is an example:

.. code-block::

   apiVersion: v1
   kind: Pod
   metadata:
     labels:
       test: liveness
     name: liveness-http
   spec:
     containers:
     - name: liveness
       image: nginx:alpine
       args:
       - /server
       livenessProbe:
         httpGet:
           path: /healthz
           port: 80
           httpHeaders:
           - name: Custom-Header
             value: Awesome
         initialDelaySeconds: 3
         periodSeconds: 3
       readinessProbe:
         exec:
           command:
             - cat
             - /tmp/healthy
         initialDelaySeconds: 5
         periodSeconds: 5
       startupProbe:
         httpGet:
           path: /healthz
           port: 80
         failureThreshold: 30
         periodSeconds: 10

For details, see :ref:`Configuring Container Health Check <cce_10_0112>`.

.. _cce_bestpractice_10017__section20910131333816:

Configuring Auto Scaling
------------------------

Auto scaling can automatically adjust the number of application pods and nodes as required. It ensures that pods and nodes can be quickly scaled to save resources and costs.

During peak hours, the following auto scaling may occur:

-  Workload scaling: When containerizing applications, you can configure resource requests and limits for the application pods to prevent them from infinitely using resources during peak hours. However, after the resource limit is reached, an error may occur. To resolve this problem, you can scale the number of pods for each workload.
-  Node scaling: After the number of pods is increased, the node resource usage may increase to a certain extent. As a result, the pods that are scaled out cannot be scheduled to that node. To resolve this problem, scale in or out nodes based on the resource usage.

For details, see :ref:`Using an HPA and a CA for Auto Scaling of Workloads and Nodes <cce_bestpractice_00282>`.

.. _cce_bestpractice_10017__section192371129144:

Configuring Logs, Monitoring Metrics, and Alarm Rules
-----------------------------------------------------

-  Logs

   -  Application logs are generated from pods. These logs include logs generated by pods that run services and Kubernetes system components like CoreDNS. CCE allows you to configure log policies to collect, manage, and analyze logs periodically to prevent logs from being over-sized.

-  Monitoring

   -  Application metrics: CCE comprehensively monitors applications in clusters. In addition to standard metrics, you can configure custom metrics that comply with the specifications for your applications to improve the observability.
