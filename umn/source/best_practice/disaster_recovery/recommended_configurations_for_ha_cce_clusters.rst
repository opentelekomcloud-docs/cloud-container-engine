:original_name: cce_bestpractice_10017.html

.. _cce_bestpractice_10017:

Recommended Configurations for HA CCE Clusters
==============================================

This section describes the recommended configurations for a Kubernetes cluster in which applications can run stably and reliably.

+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
| Item                  | Description                                                                                                                                                                                                                                                                                                                                          | Recommended Operations                                                                                                                            |
+=======================+======================================================================================================================================================================================================================================================================================================================================================+===================================================================================================================================================+
| Master node           | CCE is a hosted Kubernetes cluster service. You do not need to perform O&M on the master nodes. You can configure your cluster specifications to improve the stability and reliability.                                                                                                                                                              | -  :ref:`Deploying the Master Nodes in Different AZs <cce_bestpractice_10017__section1655395821216>`                                              |
|                       |                                                                                                                                                                                                                                                                                                                                                      | -  :ref:`Selecting a Network Model <cce_bestpractice_10017__section11239640141213>`                                                               |
|                       |                                                                                                                                                                                                                                                                                                                                                      | -  :ref:`Selecting a Service Forwarding Mode <cce_bestpractice_10017__section386335612345>`                                                       |
|                       |                                                                                                                                                                                                                                                                                                                                                      | -  :ref:`Configuring Quotas and Limits for the Cloud Service Resources and Resources in a Cluster <cce_bestpractice_10017__section1129916578355>` |
+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
| Worker node           | In a Kubernetes cluster, the data plane consists of worker nodes that can run containerized applications and transmit network traffic. When using CCE, perform O&M on worker nodes by yourself. To achieve HA, ensure the worker nodes' scalability and repairability and pay attention to the running statuses of the worker nodes' key components. | -  :ref:`Partitioning Data Disks Attached to a Node <cce_bestpractice_10017__section343391551214>`                                                |
|                       |                                                                                                                                                                                                                                                                                                                                                      | -  :ref:`Running npd <cce_bestpractice_10017__section187571360473>`                                                                               |
|                       |                                                                                                                                                                                                                                                                                                                                                      | -  :ref:`Configuring the DNS Cache <cce_bestpractice_10017__section71561443164211>`                                                               |
|                       |                                                                                                                                                                                                                                                                                                                                                      | -  :ref:`Properly Deploying CoreDNS <cce_bestpractice_10017__section1946610157435>`                                                               |
+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
| Application           | If you want your applications to be always available, especially during peak hours, run them in a scalable and elastic manner and pay attention to their running statuses.                                                                                                                                                                           | -  :ref:`Running Multiple Pods <cce_bestpractice_10017__section1014753163714>`                                                                    |
|                       |                                                                                                                                                                                                                                                                                                                                                      | -  :ref:`Configuring Resource Quotas for a Workload <cce_bestpractice_10017__section1328232471219>`                                               |
|                       |                                                                                                                                                                                                                                                                                                                                                      | -  :ref:`Deploying an Application in Multiple AZs <cce_bestpractice_10017__section4339184011374>`                                                 |
|                       |                                                                                                                                                                                                                                                                                                                                                      | -  :ref:`Deploying the System Add-ons in Multiple AZs <cce_bestpractice_10017__section17782181564512>`                                            |
|                       |                                                                                                                                                                                                                                                                                                                                                      | -  :ref:`Configuring Auto Scaling <cce_bestpractice_10017__section20910131333816>`                                                                |
|                       |                                                                                                                                                                                                                                                                                                                                                      | -  :ref:`Viewing Logs, Monitoring Metrics, and Adding Alarm Rules <cce_bestpractice_10017__section192371129144>`                                  |
+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_bestpractice_10017__section1655395821216:

Deploying the Master Nodes in Different AZs
-------------------------------------------

Multiple regions are provided for you to deploy your services, and there are different availability zones (AZs) in each region. An AZ is a collection of one or more physical data centers with independent cooling, fire extinguishing, moisture-proof, and electricity facilities in each AZ. AZs within a region are connected using high-speed optical fibers. This allows you to build cross-AZ HA systems.

When creating a cluster, enable the HA mode of the cluster and configure the distribution mode of the master nodes. The master nodes are randomly deployed in different AZs. This ensures a higher disaster recovery (DR) capability of the cluster.

You can also customize the distribution mode. The following two modes are supported:

-  **Random**: Master nodes are deployed in different AZs for DR.
-  **Custom**: Master nodes are deployed in specific AZs.

   -  **Host**: Master nodes are deployed on different hosts in the same AZ.
   -  **Custom**: Master nodes are deployed in the AZ you specify.

.. _cce_bestpractice_10017__section11239640141213:

Selecting a Network Model
-------------------------

-  Network model: CCE supports VPC, Cloud Native 2.0, and container tunnel network models. Different models have different performance and functions. For details, see :ref:`Network Models <cce_bestpractice_10016__section13189203510317>`.
-  VPC network: To enable your applications to access other cloud services like RDS, create related services in the same VPC network as your cluster. This is because services using different VPC networks are isolated from each other. If you have instances, use VPC peering connections to enable communications between VPCs.
-  Container CIDR block: Do not configure a small container CIDR block. Otherwise, the number of supported nodes will be limited.

   -  For a cluster using a VPC network, if the subnet mask of the container CIDR block is /16, there are 256 x 256 IP addresses available. If the maximum number of pods reserved on each node is 128, the maximum number of nodes supported is 512.
   -  For a cluster using a container tunnel network, if the subnet mask of the container CIDR block is /16, there are 256 x 256 IP addresses assigned to your cluster. The container CIDR block allocates 16 IP addresses to the nodes at a time by default. The maximum number of nodes supported by your cluster is 4096 (65536/16=4096).
   -  For a cluster using a Cloud Native 2.0 network, the container CIDR block is the VPC subnet, and the number of containers can be created depends on the size of the selected subnet.

-  Service CIDR block: The service CIDR block determines the upper limit of Service resources in your cluster. Evaluate your actual needs and then configure the CIDR block. A created CIDR block cannot be modified. Do not configure an excessively small one.

For details, see :ref:`Planning CIDR Blocks for a Cluster <cce_bestpractice_00004>`.

.. _cce_bestpractice_10017__section386335612345:

Selecting a Service Forwarding Mode
-----------------------------------

kube-proxy is a key component of a Kubernetes cluster. It is responsible for load balancing and forwarding between a Service and its backend pod. When using clusters, consider the potential performance problems of the forwarding mode.

CCE supports the iptables and IPVS forwarding modes.

-  IPVS allows higher throughput and faster forwarding. It applies to scenarios where the cluster scale is large or the number of Services is large.
-  iptables is the traditional kube-proxy mode. This mode applies to the scenario where the number of Services is small or there are a large number of short concurrent connections on the client. When there are more than 1000 Services in the cluster, network delay may occur.

.. _cce_bestpractice_10017__section1129916578355:

Configuring Quotas and Limits for the Cloud Service Resources and Resources in a Cluster
----------------------------------------------------------------------------------------

CCE allows you to configure resource quotas and limits for your cloud service resources and resources in your clusters. This prevents excessive use of resources. When creating your applications for CCE clusters, consider these limits and periodically review them. This will avoid scaling failures caused by insufficient quotas during application running.

-  Configuring resource quotas for cloud services: Cloud services like ECS, EVS, VPC, ELB, and SWR are also used to run the CCE clusters. If the existing resource quotas cannot meet your requirements, submit a service ticket to increase the quotas.
-  Configuring resource quotas for a cluster: You are allowed to configure the namespace-level resource quotas to limit the number of objects of a certain type created in a namespace and the total computing resources like CPU and memory consumed by the objects. For details, see :ref:`Configuring Resource Quotas <cce_10_0287>`.

.. _cce_bestpractice_10017__section343391551214:

Partitioning Data Disks Attached to a Node
------------------------------------------

If **System Component Storage** is set to **Data Disk**, one of the data disks attached to the node will be used for the container runtime and kubelet. For details about data disk allocation, see :ref:`Space Allocation of a Data Disk <cce_10_0341>`.

The default space of this date disk is 100 GiB. You can adjust the space as required. Images, system logs, and application logs are stored on data disks. Therefore, you need to evaluate the number of pods to be deployed on each node, the size of logs, images, and temporary data of each pod, as well as some reserved space for the system. For details, see :ref:`Selecting a Data Disk for the Node <cce_bestpractice_10012>`.

.. _cce_bestpractice_10017__section187571360473:

Running npd
-----------

A failure in a worker node may affect the availability of the applications. :ref:`CCE Node Problem Detector <cce_10_0132>` is used to monitor node exceptions. It helps you detect and handle latent exceptions in a timely manner. You can also customize the check items, including target node, check period, and triggering threshold. For details, see :ref:`Configuring Node Fault Detection Policies <cce_10_0659>`.

.. _cce_bestpractice_10017__section71561443164211:

Configuring the DNS Cache
-------------------------

When the number of DNS requests in a cluster increases, the load of CoreDNS increases and the following issues may occur:

-  Increased delay: CoreDNS needs to process more requests, which may slow down the DNS query and affect service performance.
-  Increased resource usage: To ensure DNS performance, CoreDNS requires higher specifications.

.. _cce_bestpractice_10017__section1946610157435:

Properly Deploying CoreDNS
--------------------------

Deploy the CoreDNS instances in different AZs and nodes to mitigate the single-node or single-AZ faults.

Ensure that the CPU and memory of the node where CoreDNS is running are not fully used. Otherwise, the Queries per second (QPS) and response of domain name resolution will be affected.

.. _cce_bestpractice_10017__section1014753163714:

Running Multiple Pods
---------------------

If your application runs in one pod, the application will be unavailable if the pod is abnormal. Use Deployments or other types of replicas to deploy your applications. Each time a pod fails or is terminated, the controller automatically restarts a new pod that has the same specifications as the original one to ensure that a specified number of pods are always running in the cluster.

When creating a workload, set the number of instances to a value greater than 2. If an instance is faulty, the remaining instances still run until Kubernetes automatically creates another pod to compensate for the loss. You can also use HPA and CA (:ref:`Using HPA and CA for Auto Scaling of Workloads and Nodes <cce_bestpractice_00282>`) to automatically scale in or out the workloads as required.

Using Containers to Isolate Processes
-------------------------------------

Containers provide process-level isolation. Each container has its own file system, network, and resource allocation. This prevents interference between different processes and avoids attacks and data leakage from malicious processes. Using containers to isolate processes can improve the reliability, security, and portability of applications.

If several processes work together, create multiple containers in a pod so that they can share the same network, PV, and other resources. Taking the init container as an example. The init containers run before the main containers are started to complete some initialization tasks like configuring environment variables, loading databases or data stores, and pulling Git repositories.

Note that multiple containers in a pod share the lifecycle of this pod. Therefore, if one container is abnormal, the entire pod will be restarted.

.. _cce_bestpractice_10017__section1328232471219:

Configuring Resource Quotas for a Workload
------------------------------------------

Configure and adjust resource requests and limits for all workloads.

If too many pods are scheduled to one node, the node will be overloaded and unable to provide services.

To avoid this problem, when deploying a pod, specify the resource request and limit required by the pod. Kubernetes then selects a node with enough idle resources for this pod. In the following example, the Nginx pod requires 1-core CPU and 1024 MiB memory. The actual usage cannot exceed 2-core CPU and 4096 MiB memory.

Kubernetes statically schedules resources. The remaining resources on each node are calculated as follows: Remaining resources on a node = Total resources on the node - Allocated resources (not resources in use). If you manually run a resource-consuming process, Kubernetes cannot detect it.

Additionally, the resource usage must be claimed for all pods. For a pod that does not claim the resource usage, after it is scheduled to a node, Kubernetes does not deduct the resources used by this pod from the node on which it is running. Other pods may still be scheduled to this node.

.. _cce_bestpractice_10017__section4339184011374:

Deploying an Application in Multiple AZs
----------------------------------------

You can run pods on nodes in multiple AZs to prevent an application from being affected by faults of a single AZ.

When creating a node, manually specify an AZ for the node.

During application deployment, configure anti-affinity policies for pods so that the scheduler can schedule pods across multiple AZs. For details, see :ref:`Implementing High Availability for Applications in CCE <cce_bestpractice_00220>`. The following is an example:

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
             preferredDuringSchedulingIgnoredDuringExecution:  # Indicates that the rule is met as much as possible. Otherwise, scheduling cannot be performed when the number of pods exceeds the number of AZs.
             - podAffinityTerm:
                 labelSelector:  # Pod label matching rule. Configure anti-affinity policies between pods and their own labels.
                   matchExpressions:
                   - key: app
                     operator: In
                     values:
                     - web-server
                 topologyKey: topology.kubernetes.io/zone  # Topology domain of the AZ where the node is located
               weight: 100

You can also use `Pod Topology Spread Constraints <https://kubernetes.io/docs/concepts/scheduling-eviction/topology-spread-constraints/>`__ to deploy pods in multiple AZs.

.. _cce_bestpractice_10017__section17782181564512:

Deploying the System Add-ons in Multiple AZs
--------------------------------------------

The Deployment pods of CCE system add-ons like CoreDNS and Everest can be deployed in multiple AZs, the same way as deploying an application. This function can satisfy different user requirements.

.. table:: **Table 1** Deployment description

   +-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
   | Mode            | Configuration Description                                                                                                                                                                                                                               | Usage Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Recommended Configuration Scenario                                                                                            |
   +=================+=========================================================================================================================================================================================================================================================+===================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================+===============================================================================================================================+
   | Preferred       | Add-on pods will have labels with the key **topology.kubernetes.io/zone** for soft anti-affinity deployment, and the anti-affinity type is **preferredDuringSchedulingIgnoredDuringExecution**.                                                         | Add-on pods will be preferentially scheduled to nodes in different AZs. If resources in some AZs are insufficient, some add-on pods may be scheduled to the same AZ which has enough resources.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | No mandatory requirements for multi-AZ DR                                                                                     |
   +-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
   | Required        | Add-on pods will have labels with the key **topology.kubernetes.io/zone** for hard anti-affinity deployment, and the anti-affinity type is **requiredDuringSchedulingIgnoredDuringExecution**.                                                          | A maximum of one pod of the same add-on can be deployed in each AZ. The number of running pods cannot exceed the number of AZs in the cluster. If the node where the add-on pod runs is faulty, pods running on the faulty node cannot be automatically migrated to other nodes in the same AZ.                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Changing number of AZs (This mode is used to prevent all pods from being scheduled to the node in the current AZ in advance.) |
   +-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+
   | Equivalent mode | Add-on pods will have labels with the key **topology.kubernetes.io/zone** for configuring topology spread constraints. The pod difference between different topology domains cannot exceed 1 for add-on pods to be evenly distributed in different AZs. | The effect of this mode is between that of the preferred mode and that of the required mode. In the equivalent mode, add-on pods can be deployed in different AZs. Additionally, multiple pods can be deployed in a single AZ when there are more pods than AZs. To use this mode, you need to plan node resources in each AZ in advance to ensure that each AZ has enough node resources for deploying pods. (If there are more than one add-on pods in a single AZ, the nodes to which the add-on pods can be scheduled in each AZ should be one more than the actual add-on pods in the current AZ.) This ensures successful deployment of add-on pods although node resources in some AZ are insufficient and smooth scheduling of add-on pods during update. | Scenarios have high requirements for DR                                                                                       |
   +-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------+

Configuring Health Check for a Container
----------------------------------------

Kubernetes automatically restarts pods that are not running properly. This prevents service interruption caused by exceptions of pods. In some cases, however, even if a pod is running, it does not mean that it can provide services properly. For example, a deadlock may occur in a process in a running pod, but Kubernetes does not automatically restart the pod because it is still running. To solve this problem, configure a liveness probe to check whether the pod is healthy. If the liveness probe detects a problem, Kubernetes will restart the pod.

You can also configure a readiness probe to check whether the pod can provide normal services. After an application container is started, it may take some time for initialization. During this process, the pod on which this container is running cannot provide services to external systems. The Services forward requests to this pod only when the readiness probe detects that the pod is ready. When a pod is faulty, the readiness probe can prevent new traffic from being forwarded to the pod.

The startup probe is used to check whether the application container is started. The startup probe ensures that the containers can start successfully before the liveness probe and readiness probe do their tasks. This ensures that the liveness probe and readiness probe do not affect the startup of containers. Configuring the startup probe ensures that the slow-start containers can be detected by the liveness probe to prevent Kubernetes from terminating them before they are started.

You can configure the preceding probes when creating an application. The following is an example:

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

Auto scaling can automatically adjust the number of application containers and nodes as required. Containers and nodes can be quickly scaled out or scaled in to save resources and costs.

Typically, two types of auto scaling may occur during peak hours:

-  Workload scaling: When deploying applications in pods, you can configure requested resources and resource limits for the pods to prevent unlimited usage of resources during peak hours. However, after the upper limit is reached, an application error may occur. To resolve this issue, scale in the number of pods to share workloads.
-  Node scaling: After the number of pods grows, the resource usage of the node may increase to a certain extent. This results in that the added pods cannot be scheduled. To solve this problem, scale in or out nodes based on the resource usage.

For details, see :ref:`Using HPA and CA for Auto Scaling of Workloads and Nodes <cce_bestpractice_00282>`.

.. _cce_bestpractice_10017__section192371129144:

Viewing Logs, Monitoring Metrics, and Adding Alarm Rules
--------------------------------------------------------

-  Logging

   -  Application logs are generated by pods. These logs include logs generated by pods in which the service containers are running and Kubernetes system components like CoreDNS. CCE allows you to configure policies for collecting, managing, and analyzing logs periodically to prevent logs from being over-sized.

-  Monitoring

   -  Metrics of the applications: CCE can comprehensively monitor applications in clusters by checking these metrics. In addition to standard metrics, you can configure custom metrics of your applications that comply with their specifications to improve the observability.
