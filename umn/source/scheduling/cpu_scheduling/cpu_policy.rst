:original_name: cce_10_0351.html

.. _cce_10_0351:

CPU Policy
==========

Application Scenarios
---------------------

By default, kubelet uses `CFS quotas <https://www.kernel.org/doc/html/next/scheduler/sched-design-CFS.html>`__ to enforce pod CPU limits. When a node runs many CPU-bound pods, the workload can move to different CPU cores depending on whether the pod is throttled and which CPU cores are available at scheduling time. Many workloads are not sensitive to this migration and thus work fine without any intervention. Some applications are CPU-sensitive. They are sensitive to:

-  CPU throttling
-  Context switching
-  Processor cache misses
-  Cross-socket memory access
-  Hyperthreads that are expected to run on the same physical CPU card

If your workloads are sensitive to any of these items, you can use Kubernetes `CPU management policies <https://kubernetes.io/docs/tasks/administer-cluster/cpu-management-policies/>`__ to allocate dedicated CPU cores (through CPU core binding) to these workloads. This will shorten scheduling latency and improve application performance. The CPU manager preferentially allocates resources on a socket and full physical cores to avoid interference.

A CPU management policy is specified by using kubelet *--cpu-manager-policy*. By default, Kubernetes supports the following policies:

-  **none**: the default policy. The **none** policy explicitly enables the existing default CPU affinity scheme, providing no affinity beyond what the OS scheduler does automatically.
-  **static**: The **static** policy allows containers in `guaranteed pods <https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/>`__ with integer CPU requests to be use dedicated CPU resources on nodes through CPU core binding.

Notes and Constraints
---------------------

The CPU management policy cannot take effect on physical cloud server nodes.

.. _cce_10_0351__section173918176434:

Enabling CPU Management for the Default Node Pool
-------------------------------------------------

When creating a cluster, you can enable CPU management in **Advanced Settings**.

-  If this function is enabled, the **static** Kubernetes policy is used, where CPU core binding is used.
-  If this function is disabled, the **none** Kubernetes policy is used, where CPU core binding is not used.

.. _cce_10_0351__section1460719557453:

Enabling CPU Management for a Custom Node Pool
----------------------------------------------

You can configure a CPU management policy in a custom node pool. After the configuration, the kubelet parameter **--cpu-manager-policy** will be automatically modified on the node in the node pool.

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Choose **Nodes** in the navigation pane and click the **Node Pools** tab on the right. Locate the target node pool and choose **More** > **Manage**.
#. On the **Manage Configurations** page, change the **cpu-manager-policy** value to **static** in the **kubelet** area.
#. Click **OK**.

Allowing Pods to Exclusively Use the CPU Resources
--------------------------------------------------

Prerequisites:

-  Enable the **static** policy on the node. For details, see :ref:`Enabling CPU Management for the Default Node Pool <cce_10_0351__section173918176434>`.
-  Both requests and limits must be configured in pods and their values must be the same integer.
-  If an init container needs to exclusively use CPUs, set its requests to the same as that of the service container. Otherwise, the service container does not inherit the CPU allocation result of the init container, and the CPU manager reserves more CPU resources than supposed. For more information, see `App Containers can't inherit Init Containers CPUs - CPU Manager Static Policy <https://github.com/kubernetes/kubernetes/issues/94220#issuecomment-868489201>`__.

You can use :ref:`node affinity scheduling <cce_10_0892>` to schedule the configured pods to the nodes where the **static** policy is enabled. In this way, the pods can exclusively use the CPU resources.

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

Verification
------------

Take a node with 8 vCPUs and 16 GiB of memory as an example. Deploy a workload whose CPU request and limit are both **2** on the node beforehand.

Log in to the node where the workload is running and check the **/var/lib/kubelet/cpu_manager_state** output.

.. code-block::

   cat /var/lib/kubelet/cpu_manager_state

The command output is as follows:

.. code-block::

   {"policyName":"static","defaultCpuSet":"0-1,4-7","entries":{"de14506d-0408-411f-bbb9-822866b58ae2":{"container-1":"2-3"}},"checksum":3744493798}

-  If the **policyName** is **static**, the policy has been configured.
-  Value **2-3** indicates the set of CPUs that can be used by containers in the pod.
