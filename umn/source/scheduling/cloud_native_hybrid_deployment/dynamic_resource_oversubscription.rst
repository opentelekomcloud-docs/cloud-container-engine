:original_name: cce_10_0384.html

.. _cce_10_0384:

Dynamic Resource Oversubscription
=================================

Many services see surges in traffic. To ensure performance and stability, resources are often requested at the maximum needed. However, the surges may ebb very shortly and resources, if not released, are wasted in non-peak hours. Especially for online jobs that request a large quantity of resources to ensure SLA, resource utilization can be as low as it gets.

Resource oversubscription is the process of using idle requested resources. Oversubscribed resources are suitable for deploying offline jobs, which focus on throughput but have low SLA requirements and can tolerate certain failures.

Hybrid deployment of online and offline jobs in a cluster can better utilize cluster resources.


.. figure:: /_static/images/en-us_image_0000002516079395.png
   :alt: **Figure 1** Resource oversubscription

   **Figure 1** Resource oversubscription

Features
--------

.. note::

   After dynamic resource oversubscription and elastic scaling are enabled in a node pool, oversubscribed resources change rapidly because the resource usage of high-priority applications changes in real time. To prevent frequent node scale-ins and scale-outs, do not consider oversubscribed resources when evaluating node scale-ins.

Hybrid deployment is supported, and CPU and memory resources can be oversubscribed. The key features are as follows:

-  Offline jobs preferentially run on oversubscribed nodes.

   If both oversubscribed and non-oversubscribed nodes exist, the former will score higher than the latter and offline jobs are preferentially scheduled to oversubscribed nodes.

-  Online jobs can use only non-oversubscribed resources if scheduled to an oversubscribed node.

   Offline jobs can use both oversubscribed and non-oversubscribed resources of an oversubscribed node.

-  In the same scheduling period, online jobs take precedence over offline jobs.

   If both online and offline jobs exist, online jobs are scheduled first. When the node resource usage exceeds the upper limit and the node requests exceed 100%, offline jobs will be evicted.

-  CPU/Memory resources can be isolation by kernel.

   CPU isolation: Online jobs can quickly preempt CPU resources of offline jobs and suppress the CPU usage of the offline jobs.

   Memory isolation: When system memory resources are used up and OOM Kill is triggered, the kernel evicts offline jobs first.

-  kubelet offline jobs obey the following admission rules:

   After a pod is scheduled to a node, kubelet starts the pod only when the node resources can meet the pod request (predicateAdmitHandler.Admit). kubelet starts the pod when both of the following conditions are met:

   -  The sum of resources requested by the pod to be started and by online running jobs is less than the node's allocatable resources.
   -  The sum of resources requested by the pod to be started and by online/offline running jobs is less than the sum of the node's allocatable resources and oversubscribed resources.

-  Resource oversubscription and hybrid deployment can be configured separately.

   Enabling hybrid deployment of a node pool also enables oversubscription by default. Nodes are then labeled with both **volcano.sh/colocation="true"** and **volcano.sh/oversubscription="true"**. To use hybrid deployment for both online and offline jobs without oversubscription, simply disable oversubscription in hybrid deployment settings. This will remove the **volcano.sh/oversubscription="true"** label.

   The following table lists the features that can be used after hybrid deployment or oversubscription is enabled.

   +-------------------+------------------+---------------------------+-----------------------------------------------------------------------------------------------------------+
   | Hybrid Deployment | Oversubscription | Oversubscription Resource | Scenario for Evicting Offline Pods                                                                        |
   +===================+==================+===========================+===========================================================================================================+
   | No                | No               | No                        | No                                                                                                        |
   +-------------------+------------------+---------------------------+-----------------------------------------------------------------------------------------------------------+
   | Yes               | No               | No                        | The actual resource usage of a node exceeds the upper limit.                                              |
   +-------------------+------------------+---------------------------+-----------------------------------------------------------------------------------------------------------+
   | No                | Yes              | Yes                       | The actual resource usage of a node exceeds the upper limit and the pod requests on the node exceed 100%. |
   +-------------------+------------------+---------------------------+-----------------------------------------------------------------------------------------------------------+
   | Yes               | Yes              | Yes                       | The actual resource usage of a node exceeds the upper limit.                                              |
   +-------------------+------------------+---------------------------+-----------------------------------------------------------------------------------------------------------+

**Scenario constraints**: Hybrid task deployment and resource oversubscription enable clusters to use compute resources more efficiently and balance resource allocation during peak and off-peak hours. This improves overall resource utilization and reduces O&M costs. With intelligent resource scheduling, CCE can handle burst resource surges, such as online service surges. For example, with a CPU QoS policy, CCE prioritizes compute resources for online tasks to ensure the stable running of key services. When memory usage temporarily increases, the underlying OS automatically reclaims memory, prioritizing the memory used by inactive data such as page cache of offline services. This may cause slight node performance fluctuations. CCE dynamically adjusts task running statuses based on resource pressure, quickly restoring overall services to a stable state. This achieves a balance between service continuity and resource efficiency.

Compatible kubelet Oversubscription
-----------------------------------

.. important::

   **Specifications**

   -  Cluster version

      -  v1.19: v1.19.16-r4 or later
      -  v1.21: v1.21.7-r0 or later
      -  v1.23: v1.23.5-r0 or later
      -  v1.25 or later

   -  Cluster type: CCE standard or Turbo
   -  Node OS: EulerOS 2.9 (kernel-4.18.0-147.5.1.6.h729.6.eulerosv2r9.x86_64) or HCE OS 2.0
   -  Node type: ECS
   -  Volcano version: 1.7.0 or later

   **Constraints**

   -  Before enabling oversubscription, ensure that the overcommit add-on is not enabled on Volcano.
   -  Modifying the label of an oversubscribed node does not affect the running pods.
   -  Running pods cannot be converted between online and offline services. To convert services, rebuild pods.
   -  If the label **volcano.sh/oversubscription=true** is configured for a node in the cluster, the **oversubscription** configuration must be added to the Volcano add-on. Otherwise, the scheduling of oversold nodes will be abnormal. Ensure that you have correctly configured labels, as the scheduler does not check the add-on and node configurations. For details, see :ref:`Table 1 <cce_10_0384__table152481219311>`.
   -  To disable oversubscription, perform the following operations:

      -  Remove the **volcano.sh/oversubscription** label from the oversubscribed node.
      -  Modify the configmap of Volcano Scheduler named **volcano-scheduler-configmap** and remove the oversubscription add-on.

   -  If you have configured the **cpu-manager-policy** to statically pin CPU cores on a node, do not assign the QoS class **Guaranteed** to offline pods. This is because offline pods may occupy CPU cores intended for online pods, potentially causing online pod startup failures and preventing offline pods from starting even after successful scheduling. To prevent this, switch the offline pods to online pods if CPU pinning is required.
   -  If **cpu-manager-policy** is set to static CPU pinning on a node, do not pin CPU cores to all online pods. This is because doing so can cause online pods to occupy all available CPU or memory resources, leaving only a small number of oversubscribed resources.

If the label **volcano.sh/oversubscription=true** is configured for a node in the cluster, the **oversubscription** configuration must be added to the Volcano add-on. Otherwise, the scheduling of oversold nodes will be abnormal. For details about the related configuration, see :ref:`Table 1 <cce_10_0384__table152481219311>`.

Ensure that you have correctly configured labels, as the scheduler does not check the add-on and node configurations.

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

#. Use kubectl to access the cluster.

#. Check the Volcano configuration.

   .. code-block::

      kubectl edit cm volcano-scheduler-configmap -n kube-system

   Check the oversubscription configuration in **volcano-scheduler-configmap**. Ensure that the add-on configuration does not contain the overcommit add-on. If **- name: overcommit** exists, delete this configuration.

   .. code-block::

      ...
      data:
        volcano-scheduler.conf: |
          actions: "allocate, backfill, preempt"   # Configure a preemption action.
          tiers:
          - plugins:
            - name: gang
              enablePreemptable: false
              enableJobStarving: false
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
      ...

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

      +-------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                 | Description                                                                                                                                                                                                                               |
      +===========================================+===========================================================================================================================================================================================================================================+
      | volcano.sh/evicting-cpu-high-watermark    | Upper limit for CPU usage. When the CPU usage of a node exceeds the specified value, offline job eviction is triggered and the node becomes unschedulable.                                                                                |
      |                                           |                                                                                                                                                                                                                                           |
      |                                           | The default value is **80**, indicating that offline job eviction is triggered when the CPU usage of a node exceeds 80%.                                                                                                                  |
      +-------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | volcano.sh/evicting-cpu-low-watermark     | Lower limit for CPU usage. When the CPU usage of a node is higher than the upper limit, offline jobs will be evicted. The node accepts the offline jobs again only when the CPU usage of the node is lower than the lower limit.          |
      |                                           |                                                                                                                                                                                                                                           |
      |                                           | The default value is **30**, indicating that offline jobs are accepted again when the CPU usage of a node is lower than 30%.                                                                                                              |
      +-------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | volcano.sh/evicting-memory-high-watermark | Upper limit for memory usage. When the memory usage of a node exceeds the specified value, offline job eviction is triggered and the node becomes unschedulable.                                                                          |
      |                                           |                                                                                                                                                                                                                                           |
      |                                           | The default value is **60**, indicating that offline job eviction is triggered when the memory usage of a node exceeds 60%.                                                                                                               |
      +-------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | volcano.sh/evicting-memory-low-watermark  | Lower limit for memory usage. When the memory usage of a node is higher than the upper limit, offline jobs will be evicted. The node accepts the offline jobs again only when the memory usage of the node is lower than the lower limit. |
      |                                           |                                                                                                                                                                                                                                           |
      |                                           | The default value is **30**, indicating that offline jobs are accepted again when the memory usage of a node is lower than 30%.                                                                                                           |
      +-------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | volcano.sh/oversubscription-types         | Oversubscribed resource type. Options:                                                                                                                                                                                                    |
      |                                           |                                                                                                                                                                                                                                           |
      |                                           | -  **cpu**: oversubscribed CPU                                                                                                                                                                                                            |
      |                                           | -  **memory**: oversubscribed memory                                                                                                                                                                                                      |
      |                                           | -  **cpu,memory**: oversubscribed CPU and memory                                                                                                                                                                                          |
      |                                           |                                                                                                                                                                                                                                           |
      |                                           | The default value is **cpu,memory**.                                                                                                                                                                                                      |
      +-------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Create resources at a high- and low-priorityClass, respectively.

   .. code-block::

      cat <<EOF | kubectl apply -f -

      apiVersion: scheduling.k8s.io/v1
      description: Used for high priority pods
      kind: PriorityClass
      metadata:
        name: volcano-production
      preemptionPolicy: PreemptLowerPriority
      value: 999999
      ---
      apiVersion: scheduling.k8s.io/v1
      description: Used for low priority pods
      kind: PriorityClass
      metadata:
        name: volcano-free
      preemptionPolicy: PreemptLowerPriority
      value: -90000

      EOF

#. Deploy online and offline jobs and configure PriorityClasses for these jobs.

   The **volcano.sh/qos-level** annotation needs to be added to distinguish offline jobs. The value is an integer ranging from -7 to 7. If the value is less than 0, the job is an offline job. If the value is greater than or equal to 0, the job is an online job. You do not need to set this annotation for online jobs. For both online and offline jobs, set **schedulerName** to **volcano** to enable Volcano.

   .. note::

      The priorities between online jobs and between offline jobs are not differentiated, and the value validity is not verified. If the value of **volcano.sh/qos-level** of an offline job is not a negative integer ranging from -7 to 0, the job is processed as an online job.

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
              volcano.sh/qos-level: "-1"       # Offline job annotation
          spec:
            schedulerName: volcano             # Volcano is used.
            priorityClassName: volcano-free         # volcano-free priorityClass
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
            schedulerName: volcano          # Volcano is used.
            priorityClassName: volcano-production   # volcano-production priorityClass
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

   In the preceding command, CPU and memory are in the unit of m CPU cores and bytes, respectively.

Deployment Example
------------------

The following uses an example to describe how to deploy online and offline jobs in hybrid mode.

#. Configure a cluster with two nodes, one oversubscribed and the other non-oversubscribed.

   .. code-block::

      # kubectl get node
      NAME           STATUS   ROLES    AGE    VERSION
      192.168.0.173   Ready    <none>   4h58m   v1.19.16-r2-CCE22.5.1
      192.168.0.3     Ready    <none>   148m    v1.19.16-r2-CCE22.5.1

   -  192.168.0.173 is an oversubscribed node (with the **volcano.sh/oversubscription=true** label).
   -  192.168.0.3 is a non-oversubscribed node (without the **volcano.sh/oversubscription=true** label).

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
              volcano.sh/qos-level: "-1"       # Offline job label
          spec:
            schedulerName: volcano             # Volcano is used.
            priorityClassName: volcano-free         # volcano-free priorityClass
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
            schedulerName: volcano                 # Volcano is used.
            priorityClassName: volcano-production          # volcano-production priorityClass
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
             affinity:                             # Submit an online job to an oversubscribed node.
              nodeAffinity:
                requiredDuringSchedulingIgnoredDuringExecution:
                  nodeSelectorTerms:
                  - matchExpressions:
                    - key: kubernetes.io/hostname
                      operator: In
                      values:
                      - 192.168.0.173
            schedulerName: volcano                 # Volcano is used.
            priorityClassName: volcano-production          # volcano-production priorityClass
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

   Check the oversubscribed node with IP address 192.168.0.173. It is found that resources are oversubscribed, where there are 2343m CPU cores and 3073653200 bytes of memory. Additionally, the CPU allocation rate exceeded 100%.

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

Handling Suggestions
--------------------

-  After kubelet of the oversubscribed node is restarted, the resource view of Volcano Scheduler is not synchronized with that of kubelet. As a result, OutOfCPU occurs in some newly scheduled jobs, which is normal. After a period of time, Volcano Scheduler can properly schedule online and offline jobs.

-  After online and offline jobs are submitted, you are not advised to dynamically change the job type (adding or deleting annotation volcano.sh/qos-level: "-1") because the current kernel does not support the change of an offline job to an online job.

-  CCE collects the resource usage (CPU/memory) of all pods running on a node based on the status information in the cgroups system. The resource usage may be different from the monitored resource usage, for example, the resource statistics displayed by running the **top** command.

-  You can add oversubscribed resources (such as CPU and memory) at any time.

   You can reduce the oversubscribed resource types only when the resource allocation rate does not exceed 100%.

-  If an offline job is deployed on a node ahead of an online job and the online job cannot be scheduled due to insufficient resources, configure a higher priorityClass for the online job than that for the offline job.

-  If there are only online jobs on a node and the eviction threshold is reached, the offline jobs that are scheduled to the current node will be evicted soon. This is normal.
