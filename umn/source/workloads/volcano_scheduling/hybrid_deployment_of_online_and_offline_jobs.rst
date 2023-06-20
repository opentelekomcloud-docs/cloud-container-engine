:original_name: cce_10_0384.html

.. _cce_10_0384:

Hybrid Deployment of Online and Offline Jobs
============================================

Online and Offline Jobs
-----------------------

Jobs can be classified into online jobs and offline jobs based on whether services are always online.

-  **Online job**: Such jobs run for a long time, with regular traffic surges, tidal resource requests, and high requirements on SLA, such as advertising and e-commerce services.
-  **Offline jobs**: Such jobs run for a short time, have high computing requirements, and can tolerate high latency, such as AI and big data services.

Resource Oversubscription and Hybrid Deployment
-----------------------------------------------

Many services see surges in traffic. To ensure performance and stability, resources are often requested at the maximum needed. However, the surges may ebb very shortly and resources, if not released, are wasted in non-peak hours. Especially for online jobs that request a large quantity of resources to ensure SLA, resource utilization can be as low as it gets.

Resource oversubscription is the process of making use of idle requested resources. Oversubscribed resources are suitable for deploying offline jobs, which focus on throughput but have low SLA requirements and can tolerate certain failures.

Hybrid deployment of online and offline jobs in a cluster can better utilize cluster resources.


.. figure:: /_static/images/en-us_image_0000001568902489.png
   :alt: **Figure 1** Resource oversubscription

   **Figure 1** Resource oversubscription

Oversubscription for Hybrid Deployment
--------------------------------------

Hybrid deployment is supported, and CPU and memory resources can be oversubscribed. The key features are as follows:

-  Offline jobs preferentially run on oversubscribed nodes.

   If both oversubscribed and non-oversubscribed nodes exist, the former will score higher than the latter and offline jobs are preferentially scheduled to oversubscribed nodes.

-  Online jobs can use only non-oversubscribed resources if scheduled to an oversubscribed node.

   Offline jobs can use both oversubscribed and non-oversubscribed resources of an oversubscribed node.

-  In the same scheduling period, online jobs take precedence over offline jobs.

   If both online and offline jobs exist, online jobs are scheduled first. When the node resource usage exceeds the upper limit and the node requests exceed 100%, offline jobs will be evicted.

-  CPU/memory isolation is provided by kernels.

   CPU isolation: Online jobs can quickly preempt CPU resources of offline jobs and suppress the CPU usage of the offline jobs.

   Memory isolation: When system memory resources are used up and OOM Kill is triggered, the kernel evicts offline jobs first.

-  kubelet offline jobs admission rules:

   After the the pod is scheduled to a node, kubelet starts the pod only when the node resources can meet the pod request (predicateAdmitHandler.Admit). kubelet starts the pod when both of the following conditions are met:

   -  The total request of pods to be started and online running jobs < allocatable nodes
   -  The total request of pods to be started and online/offline running job < allocatable nodes+oversubscribed nodes

-  Resource oversubscription and hybrid deployment:

   If only hybrid deployment is used, you need to configure the label **volcano.sh/colocation=true** for the node and delete the node label **volcano.sh/oversubscription** or set its value to **false**.

   If the label **volcano.sh/colocation=true** is configured for a node, hybrid deployment is enabled. If the label **volcano.sh/oversubscription=true** is configured, resource oversubscription is enabled. The following table lists the available feature combinations after hybrid deployment or resource oversubscription is enabled.

   +--------------------------------------------------------+----------------------------------------------------------------------+-------------------------------+----------------------------------------------------------------------------------------+
   | Hybrid Deployment Enabled (volcano.sh/colocation=true) | Resource oversubscription Enabled (volcano.sh/oversubscription=true) | Use Oversubscribed Resources? | Conditions for Evicting Offline Pods                                                   |
   +========================================================+======================================================================+===============================+========================================================================================+
   | No                                                     | No                                                                   | No                            | None                                                                                   |
   +--------------------------------------------------------+----------------------------------------------------------------------+-------------------------------+----------------------------------------------------------------------------------------+
   | Yes                                                    | No                                                                   | No                            | The node resource usage exceeds the high threshold.                                    |
   +--------------------------------------------------------+----------------------------------------------------------------------+-------------------------------+----------------------------------------------------------------------------------------+
   | No                                                     | Yes                                                                  | Yes                           | The node resource usage exceeds the high threshold, and the node request exceeds 100%. |
   +--------------------------------------------------------+----------------------------------------------------------------------+-------------------------------+----------------------------------------------------------------------------------------+
   | Yes                                                    | Yes                                                                  | Yes                           | The node resource usage exceeds the high threshold.                                    |
   +--------------------------------------------------------+----------------------------------------------------------------------+-------------------------------+----------------------------------------------------------------------------------------+

Notes and Constraints
---------------------

**Specifications**

-  Kubernetes version:

   -  v1.19: v1.19.16-r4 or later
   -  v1.21: v1.21.7-r0 or later
   -  v1.23: v1.23.5-r0 or later
   -  v1.25 or later

-  Cluster type: CCE or CCE Turbo
-  Node OS: EulerOS 2.9 (kernel-4.18.0-147.5.1.6.h729.6.eulerosv2r9.x86_64)
-  Node type: ECS
-  volcano add-on version: 1.7.0 or later

**Constraints**

-  Before enabling the volcano oversubscription plug-in, ensure that the overcommit plug-in is not enabled.
-  Modifying the label of an oversubscribed node does not affect the running pods.
-  Running pods cannot be converted between online and offline services. To convert services, you need to rebuild pods.
-  If the label **volcano.sh/oversubscription=true** is configured for a node in the cluster, the **oversubscription** configuration must be added to the volcano add-on. Otherwise, the scheduling of oversubscribed nodes will be abnormal. Ensure that you have correctly configure labels because the scheduler does not check the add-on and node configurations. For details about the labels, see :ref:`Configuring Oversubscription Labels for Scheduling <cce_10_0384__section1940910414220>`.
-  To disable oversubscription, perform the following operations:

   -  Remove the **volcano.sh/oversubscription** label from the oversubscribed node.
   -  Set **over-subscription-resource** to **false**.
   -  Modify the configmap of the volcano scheduler named **volcano-scheduler-configmap** and remove the oversubscription add-on.

-  If **cpu-manager-policy** is set to static core binding on a node, do not assign the QoS class of Guaranteed to offline pods. If core binding is required, change the pods to online pods. Otherwise, offline pods may occupy the CPUs of online pods, causing online pod startup failures, and offline pods fail to be started although they are successfully scheduled.
-  If **cpu-manager-policy** is set to static core binding on a node, do not bind cores to all online pods. Otherwise, online pods occupy all CPU or memory resources, leaving a small number of oversubscribed resources.

.. _cce_10_0384__section1940910414220:

Configuring Oversubscription Labels for Scheduling
--------------------------------------------------

If the label **volcano.sh/oversubscription=true** is configured for a node in the cluster, the **oversubscription** configuration must be added to the volcano add-on. Otherwise, the scheduling of oversubscribed nodes will be abnormal. For details about the related configuration, see :ref:`Table 1 <cce_10_0384__table152481219311>`.

Ensure that you have correctly configure labels because the scheduler does not check the add-on and node configurations.

.. _cce_10_0384__table152481219311:

.. table:: **Table 1** Configuring oversubscription labels for scheduling

   +----------------------------+--------------------------------+----------------------------------------------------+
   | Oversubscription in Add-on | Oversubscription Label on Node | Scheduling                                         |
   +============================+================================+====================================================+
   | Yes                        | Yes                            | Triggered by oversubscription                      |
   +----------------------------+--------------------------------+----------------------------------------------------+
   | Yes                        | No                             | Triggered                                          |
   +----------------------------+--------------------------------+----------------------------------------------------+
   | No                         | No                             | Triggered                                          |
   +----------------------------+--------------------------------+----------------------------------------------------+
   | No                         | Yes                            | Not triggered or failed. Avoid this configuration. |
   +----------------------------+--------------------------------+----------------------------------------------------+

Using Hybrid Deployment
-----------------------

#. Configure the volcano add-on.

   a. Use kubectl to connect to the cluster.

   b. Install the volcano plug-in and add the **oversubscription** plug-in to **volcano-scheduler-configmap**. Ensure that the plug-in configuration does not contain the **overcommit** plug-in. If **- name: overcommit** exists, delete this configuration.

      .. code-block::

         # kubectl edit cm volcano-scheduler-configmap -n kube-system
         apiVersion: v1
         data:
           volcano-scheduler.conf: |
             actions: "enqueue, allocate, backfill"
             tiers:
             - plugins:
               - name: gang
               - name: priority
               - name: conformance
               - name: oversubscription
             - plugins:
               - name: drf
               - name: predicates
               - name: nodeorder
               - name: binpack
             - plugins:
               - name: cce-gpu-topology-predicate
               - name: cce-gpu-topology-priority
               - name: cce-gpu

#. Enable the node oversubscription feature.

   A label can be configured to use oversubscribed resources only after the oversubscription feature is enabled for a node. Related nodes can be created only in a node pool. To enable the oversubscription feature, perform the following steps:

   a. Create a node pool.
   b. Choose **More** > **Manage** in the **Operation** column of the created node pool.
   c. In the **Manage Component** window that is displayed, set **over-subscription-resource** under **kubelet** to **true** and click **OK**.

   |image1|

#. Set the node oversubscription label.

   The **volcano.sh/oversubscription** label needs to be configured for an oversubscribed node. If this label is set for a node and the value is **true**, the node is an oversubscribed node. Otherwise, the node is not an oversubscribed node.

   .. code-block::

      kubectl label node 192.168.0.0 volcano.sh/oversubscription=true

   An oversubscribed node also supports the oversubscription thresholds, as listed in :ref:`Table 2 <cce_10_0384__table1853397191112>`. For example:

   .. code-block::

      kubectl annotate node 192.168.0.0 volcano.sh/evicting-cpu-high-watermark=70

   Querying the node information

   .. code-block::

      # kubectl describe node 192.168.0.0
      Name:             192.168.0.0
      Roles:              <none>
      Labels:           ...
                        volcano.sh/oversubscription=true
      Annotations:      ...
                        volcano.sh/evicting-cpu-high-watermark: 70

   .. _cce_10_0384__table1853397191112:

   .. table:: **Table 2** Node oversubscription annotations

      +-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
      | Name                                      | Description                                                                                                                        |
      +===========================================+====================================================================================================================================+
      | volcano.sh/evicting-cpu-high-watermark    | When the CPU usage of a node exceeds the specified value, offline job eviction is triggered and the node becomes unschedulable.    |
      |                                           |                                                                                                                                    |
      |                                           | The default value is **80**, indicating that offline job eviction is triggered when the CPU usage of a node exceeds 80%.           |
      +-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
      | volcano.sh/evicting-cpu-low-watermark     | After eviction is triggered, the scheduling starts again when the CPU usage of a node is lower than the specified value.           |
      |                                           |                                                                                                                                    |
      |                                           | The default value is **30**, indicating that scheduling starts again when the CPU usage of a node is lower than 30%.               |
      +-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
      | volcano.sh/evicting-memory-high-watermark | When the memory usage of a node exceeds the specified value, offline job eviction is triggered and the node becomes unschedulable. |
      |                                           |                                                                                                                                    |
      |                                           | The default value is **60**, indicating that offline job eviction is triggered when the memory usage of a node exceeds 60%.        |
      +-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
      | volcano.sh/evicting-memory-low-watermark  | After eviction is triggered, the scheduling starts again when the memory usage of a node is lower than the specified value.        |
      |                                           |                                                                                                                                    |
      |                                           | The default value is **30**, indicating that the scheduling starts again when the memory usage of a node is less than 30%.         |
      +-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+
      | volcano.sh/oversubscription-types         | Oversubscribed resource type. The options are as follows:                                                                          |
      |                                           |                                                                                                                                    |
      |                                           | -  CPU (oversubscribed CPU)                                                                                                        |
      |                                           | -  memory (oversubscribed memory)                                                                                                  |
      |                                           | -  cpu,memory (oversubscribed CPU and memory)                                                                                      |
      |                                           |                                                                                                                                    |
      |                                           | The default value is **cpu,memory**.                                                                                               |
      +-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------+

#. Deploy online and offline jobs.

   The **volcano.sh/qos-level** label needs to be added to annotation to distinguish offline jobs. The value is an integer ranging from -7 to 7. If the value is less than 0, the job is an offline job. If the value is greater than or equal to 0, the job is a high-priority job, that is, online job. You do not need to set this label for online jobs. For both online and offline jobs, set **schedulerName** to **volcano** to enable the Volcano scheduler.

   .. note::

      The priorities of online/online and offline/offline jobs are not differentiated, and the value validity is not verified. If the value of **volcano.sh/qos-level** of an offline job is not a negative integer ranging from -7 to 0, the job is processed as an online job.

   For an offline job:

   .. code-block::

      kind: Deployment
      apiVersion: apps/v1
      spec:
        replicas: 4
        template:
          metadata:
            annotations:
              metrics.alpha.kubernetes.io/custom-endpoints: '[{"api":"","path":"","port":"","names":""}]'
              volcano.sh/qos-level: "-1" # Offline job label
          spec:
            schedulerName: volcano             # The Volcano scheduler is used.
            ...

   For an online job:

   .. code-block::

      kind: Deployment
      apiVersion: apps/v1
      spec:
        replicas: 4
        template:
          metadata:
            annotations:
              metrics.alpha.kubernetes.io/custom-endpoints: '[{"api":"","path":"","port":"","names":""}]'
          spec:
            schedulerName: volcano          # The Volcano scheduler is used.
            ...

#. Run the following command to check the number of oversubscribed resources and the resource usage:

   kubectl describe node *<nodeIP>*

   .. code-block::

      # kubectl describe node 192.168.0.0
      Name:             192.168.0.0
      Roles:              <none>
      Labels:           ...
                        volcano.sh/oversubscription=true
      Annotations:      ...
                        volcano.sh/oversubscription-cpu: 2335
                        volcano.sh/oversubscription-memory: 341753856
      Allocatable:
        cpu:               3920m
        memory:            6263988Ki
      Allocated resources:
        (Total limits may be over 100 percent, i.e., overcommitted.)
        Resource           Requests      Limits
        --------           --------      ------
        cpu                 4950m (126%)  4950m (126%)
        memory             1712Mi (27%)  1712Mi (27%)

Hybrid Deployment Example
-------------------------

The following uses an example to describe how to deploy online and offline jobs in hybrid mode.

#. Assume that a cluster has two nodes: one oversubscribed node and one non-oversubscribed node.

   .. code-block::

      # kubectl get node
      NAME           STATUS   ROLES    AGE    VERSION
      192.168.0.173   Ready    <none>   4h58m   v1.19.16-r2-CCE22.5.1
      192.168.0.3     Ready    <none>   148m    v1.19.16-r2-CCE22.5.1

   -  192.168.0.173 is an oversubscribed node (with the **volcano.sh/oversubscirption=true** label).
   -  192.168.0.3 is a non-oversubscribed node (without the **volcano.sh/oversubscirption=true** label).

   .. code-block::

      # kubectl describe node 192.168.0.173
      Name:               192.168.0.173
      Roles:              <none>
      Labels:             beta.kubernetes.io/arch=amd64
                          ...
                          volcano.sh/oversubscription=true

#. Submit offline job creation requests. If resources are sufficient, all offline jobs will be scheduled to the oversubscribed node.

   The offline job template is as follows:

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: offline
        namespace: default
      spec:
        replicas: 2
        selector:
          matchLabels:
            app: offline
        template:
          metadata:
            labels:
              app: offline
            annotations:
              volcano.sh/qos-level: "-1"       #Offline job label
          spec:
            schedulerName: volcano             # The Volcano scheduler is used.
            containers:
              - name: container-1
                image: nginx:latest
                imagePullPolicy: IfNotPresent
                resources:
                  requests:
                    cpu: 500m
                    memory: 512Mi
                  limits:
                    cpu: "1"
                    memory: 512Mi
            imagePullSecrets:
              - name: default-secret

   Offline jobs are scheduled to the oversubscribed node.

   .. code-block::

      # kubectl get pod -o wide
      NAME                      READY   STATUS   RESTARTS  AGE     IP             NODE
      offline-69cdd49bf4-pmjp8   1/1    Running   0         5s    192.168.10.178   192.168.0.173
      offline-69cdd49bf4-z8kxh   1/1    Running   0         5s    192.168.10.131   192.168.0.173

#. Submit online job creation requests. If resources are sufficient, the online jobs will be scheduled to the non-oversubscribed node.

   The online job template is as follows:

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: online
        namespace: default
      spec:
        replicas: 2
        selector:
          matchLabels:
            app: online
        template:
          metadata:
            labels:
              app: online
          spec:
            schedulerName: volcano                 # The Volcano scheduler is used.
            containers:
              - name: container-1
                image: resource_consumer:latest
                imagePullPolicy: IfNotPresent
                resources:
                  requests:
                    cpu: 1400m
                    memory: 512Mi
                  limits:
                    cpu: "2"
                    memory: 512Mi
            imagePullSecrets:
              - name: default-secret

   Online jobs are scheduled to the non-oversubscribed node.

   .. code-block::

      # kubectl get pod -o wide
      NAME                   READY   STATUS   RESTARTS  AGE     IP             NODE
      online-ffb46f656-4mwr6  1/1    Running   0         5s    192.168.10.146   192.168.0.3
      online-ffb46f656-dqdv2   1/1    Running   0         5s    192.168.10.67   192.168.0.3

#. Improve the resource usage of the oversubscribed node and observe whether offline job eviction is triggered.

   Deploy online jobs to the oversubscribed node (192.168.0.173).

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: online
        namespace: default
      spec:
        replicas: 2
        selector:
          matchLabels:
            app: online
        template:
          metadata:
            labels:
              app: online
          spec:
             affinity:                              # Submit an online job to an oversubscribed node.
              nodeAffinity:
                requiredDuringSchedulingIgnoredDuringExecution:
                  nodeSelectorTerms:
                  - matchExpressions:
                    - key: kubernetes.io/hostname
                      operator: In
                      values:
                      - 192.168.0.173
            schedulerName: volcano                 # The Volcano scheduler is used.
            containers:
              - name: container-1
                image: resource_consumer:latest
                imagePullPolicy: IfNotPresent
                resources:
                  requests:
                    cpu: 700m
                    memory: 512Mi
                  limits:
                    cpu: 700m
                    memory: 512Mi
            imagePullSecrets:
              - name: default-secret

   Submit the online or offline jobs to the oversubscribed node (192.168.0.173) at the same time.

   .. code-block::

      # kubectl get pod -o wide
      NAME                     READY   STATUS   RESTARTS  AGE     IP             NODE
      offline-69cdd49bf4-pmjp8  1/1     Running    0      13m   192.168.10.178   192.168.0.173
      offline-69cdd49bf4-z8kxh  1/1     Running     0      13m   192.168.10.131   192.168.0.173
      online-6f44bb68bd-b8z9p  1/1     Running     0     3m4s   192.168.10.18   192.168.0.173
      online-6f44bb68bd-g6xk8  1/1     Running     0     3m12s   192.168.10.69   192.168.0.173

   Observe the oversubscribed node (192.168.0.173). You can find that oversubscribed resources exist and the CPU allocation rate exceeds 100%.

   .. code-block::

      # kubectl describe node 192.168.0.173
      Name:              192.168.0.173
      Roles:              <none>
      Labels:              …
                          volcano.sh/oversubscription=true
      Annotations:         …
                          volcano.sh/oversubscription-cpu: 2343
                          volcano.sh/oversubscription-memory: 3073653200
                          …
      Allocated resources:
        (Total limits may be over 100 percent, i.e., overcommitted.)
        Resource               Requests      Limits
        --------               --------        ------
        cpu                    4750m (121%)  7350m (187%)
        memory                 3760Mi (61%)  4660Mi (76%)
                               …

   Increase the CPU usage of online jobs on the node. Offline job eviction is triggered.

   .. code-block::

      # kubectl get pod -o wide
      NAME                     READY   STATUS   RESTARTS  AGE     IP             NODE
      offline-69cdd49bf4-bwdm7  1/1    Running   0       11m   192.168.10.208  192.168.0.3
      offline-69cdd49bf4-pmjp8   0/1    Evicted    0       26m   <none>         192.168.0.173
      offline-69cdd49bf4-qpdss   1/1     Running   0       11m   192.168.10.174  192.168.0.3
      offline-69cdd49bf4-z8kxh   0/1     Evicted    0       26m   <none>        192.168.0.173
      online-6f44bb68bd-b8z9p   1/1     Running   0       24m   192.168.10.18  192.168.0.173
      online-6f44bb68bd-g6xk8   1/1     Running   0       24m   192.168.10.69  192.168.0.173

#. Log in to the CCE console and access the cluster console.

#. In the navigation pane on the left, choose **Nodes**. Click the **Node Pools** tab. When creating or updating a node pool, enable hybrid deployment of online and offline services in **Advanced Settings**.

#. In the navigation pane on the left, choose **Add-ons**. Click **Install** under volcano. In the **Advanced Settings** area, set **colocation_enable** to **true** to enable hybrid deployment of online and offline services. For details about the installation, see :ref:`volcano <cce_10_0193>`.

   If the volcano add-on has been installed, click **Edit** to view or modify the parameter **colocation_enable**.

#. Enable CPU Burst.

   After confirming that the volcano add-on is working, run the following command to edit the parameter **configmap** of **volcano-agent-configuration** in the namespace **kube-system**. If **enable** is set to **true**, CPU Burst is enabled. If **enable** is set to **false**, CPU Burst is disabled.

   .. code-block::

      kubectl edit configmap -nkube-system volcano-agent-configuration

   Example:

   .. code-block::

      cpuBurstConfig:
        enable: true

#. Deploy a workload in a node pool where hybrid deployment has been enabled. Take Nginx as an example. Set **cpu** under **requests** to **2** and **cpu** under **limits** to **4**, and create a Service that can be accessed in the cluster for the workload.

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: nginx
        namespace: default
      spec:
        replicas: 2
        selector:
          matchLabels:
            app: nginx
        template:
          metadata:
            labels:
              app: nginx
            annotations:
              volcano.sh/enable-quota-burst=true
              volcano.sh/quota-burst-time=200000
          spec:
            containers:
            - name: container-1
              image: nginx:latest
              resources:
                limits:
                  cpu: "4"
                requests:
                  cpu: "2"
            imagePullSecrets:
              - name: default-secret
      ---
      apiVersion: v1
      kind: Service
      metadata:
        name: nginx
        namespace: default
        labels:
          app: nginx
      spec:
        selector:
          app: nginx
        ports:
          - name: cce-service-0
            targetPort: 80
            nodePort: 0
            port: 80
            protocol: TCP
        type: ClusterIP

   +------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Annotation                         | Mandatory             | Description                                                                                                                                                                                                                                                                                                                                     |
   +====================================+=======================+=================================================================================================================================================================================================================================================================================================================================================+
   | volcano.sh/enable-quota-burst=true | Yes                   | CPU Burst is enabled for the workload.                                                                                                                                                                                                                                                                                                          |
   +------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | volcano.sh/quota-burst-time=200000 | No                    | To ensure CPU scheduling stability and reduce contention when multiple containers encounter CPU bursts at the same time, the default **CPU Burst** value is the same as the **CPU Quota** value. That is, a container can use a maximum of twice the **CPU Limit** value. By default, **CPU Burst** is set for all service containers in a pod. |
   |                                    |                       |                                                                                                                                                                                                                                                                                                                                                 |
   |                                    |                       | In this example, the **CPU Limit** of the container is **4**, that is, the default value is **400,000** (1 core = 100,000), indicating that a maximum of four additional cores can be used after the **CPU Limit** value is reached.                                                                                                            |
   +------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Verify CPU Burst.

   You can use the wrk tool to increase load of the workload and observe the service latency, traffic limiting, and CPU limit exceeding when CPU Burst is enabled and disabled, respectively.

   a. Run the following command to increase load of the pod. *$service_ip* indicates the service IP address associated with the pod.

      .. code-block::

         # You need to download and install the wrk tool on the node.
         # The Gzip compression module is enabled in the Apache configuration to simulate the computing logic for the server to process requests.
         # Run the following command to increase the load. Note that you need to change the IP address of the target application.
         wrk -H "Accept-Encoding: deflate, gzip" -t 4 -c 28 -d 120  --latency --timeout 2s http://$service_ip

   b. Obtain the pod ID.

      .. code-block::

         kubectl get pods -n <namespace> <pod-name> -o jsonpath='{.metadata.uid}'

   c. You can run the following command on the node to view the traffic limiting status and CPU limit exceeding status. In the command, *$PodID* indicates the pod ID.

      .. code-block::

         $cat /sys/fs/cgroup/cpuacct/kubepods/$PodID/cpu.stat
         nr_periods 0  # Number of scheduling periods
         nr_throttled 0  # Traffic limiting times
         throttled_time 0  # Traffic limiting duration (ns)
         nr_bursts 0 # CPU Limit exceeding times
         burst_time 0 # Total Limit exceeding duration

      .. table:: **Table 3** Result summary in this example

         +-----------------------+-------------+------------------------+---------------------------+-----------------------+--------------------------------+
         | CPU Burst             | P99 Latency | nr_throttled           | throttled_time            | nr_bursts             | bursts_time                    |
         |                       |             |                        |                           |                       |                                |
         |                       |             | Traffic Limiting Times | Traffic Limiting Duration | Limit Exceeding Times | Total Limit Exceeding Duration |
         +=======================+=============+========================+===========================+=======================+================================+
         | CPU Burst not enabled | 2.96 ms     | 986                    | 14.3s                     | 0                     | 0                              |
         +-----------------------+-------------+------------------------+---------------------------+-----------------------+--------------------------------+
         | CPU Burst enabled     | 456 µs      | 0                      | 0                         | 469                   | 3.7s                           |
         +-----------------------+-------------+------------------------+---------------------------+-----------------------+--------------------------------+

Handling Suggestions
--------------------

-  After kubelet of the oversubscribed node is restarted, the resource view of the Volcano scheduler is not synchronized with that of kubelet. As a result, OutOfCPU occurs in some newly scheduled jobs, which is normal. After a period of time, the Volcano scheduler can properly schedule online and offline jobs.

-  After online and offline jobs are submitted, you are not advised to dynamically change the job type (adding or deleting annotation volcano.sh/qos-level: "-1") because the current kernel does not support the change of an offline job to an online job.

-  CCE collects the resource usage (CPU/memory) of all pods running on a node based on the status information in the cgroups system. The resource usage may be different from the monitored resource usage, for example, the resource statistics displayed by running the **top** command.

-  You can add oversubscribed resources (such as CPU and memory) at any time.

   You can reduce the oversubscribed resource types only when the resource allocation rate does not exceed 100%.

.. |image1| image:: /_static/images/en-us_image_0000001518062608.png
