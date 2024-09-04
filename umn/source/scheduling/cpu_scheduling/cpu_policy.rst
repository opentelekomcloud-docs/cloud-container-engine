:original_name: cce_10_0351.html

.. _cce_10_0351:

CPU Policy
==========

Scenarios
---------

By default, kubelet uses `CFS quotas <https://www.kernel.org/doc/html/latest/scheduler/sched-design-CFS.html>`__ to enforce pod CPU limits. When a node runs many CPU-bound pods, the workload can move to different CPU cores depending on whether the pod is throttled and which CPU cores are available at scheduling time. Many workloads are not sensitive to this migration and thus work fine without any intervention. Some applications are CPU-sensitive. They are sensitive to:

-  CPU throttling
-  Context switching
-  Processor cache misses
-  Cross-socket memory access
-  Hyperthreads that are expected to run on the same physical CPU card

If your workloads are sensitive to any of these items and CPU cache affinity and scheduling latency significantly affect workload performance, kubelet allows alternative CPU management policies (CPU binding) to determine some placement preferences on the node. The CPU manager preferentially allocates resources on a socket and full physical cores to avoid interference.

Notes and Constraints
---------------------

The CPU management policy cannot take effect on physical cloud server nodes.

.. _cce_10_0351__section173918176434:

Enabling the CPU Management Policy
----------------------------------

A `CPU management policy <https://kubernetes.io/docs/tasks/administer-cluster/cpu-management-policies/>`__ is specified by the kubelet flag **--cpu-manager-policy**. By default, Kubernetes supports the following policies:

-  Disabled (**none**): the default policy. The **none** policy explicitly enables the existing default CPU affinity scheme, providing no affinity beyond what the OS scheduler does automatically.
-  Enabled (**static**): The **static** policy allows containers in `guaranteed <https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/>`__ pods with integer GPU requests to be granted increased CPU affinity and exclusivity on the node.

When creating a cluster, you can configure the CPU management policy in **Advanced Settings**.

You can also configure the policy in a node pool. The configuration will change the kubelet flag **--cpu-manager-policy** on the node. Log in to the CCE console, click the cluster name, access the cluster details page, and choose **Nodes** in the navigation pane. On the page displayed, click the **Node Pools** tab. Choose **More** > **Manage** in the **Operation** column of the target node pool, and change the value of **cpu-manager-policy** to **static**.

Allowing Pods to Exclusively Use the CPU Resources
--------------------------------------------------

Prerequisites:

-  Enable the **static** policy on the node. For details, see :ref:`Enabling the CPU Management Policy <cce_10_0351__section173918176434>`.
-  Both requests and limits must be configured in pods and their values must be the same integer.
-  If an init container needs to exclusively use CPUs, set its requests to the same as that of the service container. Otherwise, the service container does not inherit the CPU allocation result of the init container, and the CPU manager reserves more CPU resources than supposed. For more information, see `App Containers can't inherit Init Containers CPUs - CPU Manager Static Policy <https://github.com/kubernetes/kubernetes/issues/94220#issuecomment-868489201>`__.

You can use :ref:`Scheduling Policies (Affinity/Anti-affinity) <cce_10_0232>` to schedule the configured pods to the nodes where the **static** policy is enabled. In this way, the pods can exclusively use the CPU resources.

Example YAML:

.. code-block::

   kind: Deployment
   apiVersion: apps/v1
   metadata:
     name: test
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: test
     template:
       metadata:
         labels:
           app: test
       spec:
         containers:
           - name: container-1
             image: nginx:alpine
             resources:
               requests:
                 cpu: 2           # The value must be an integer and must be the same as that in limits.
                 memory: 2048Mi
               limits:
                 cpu: 2           # The value must be an integer and must be the same as that in requests.
                 memory: 2048Mi
         imagePullSecrets:
           - name: default-secret
