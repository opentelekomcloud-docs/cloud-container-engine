:original_name: cce_10_0351.html

.. _cce_10_0351:

Binding CPU Cores
=================

By default, kubelet uses `CFS quotas <https://www.kernel.org/doc/html/latest/scheduler/sched-design-CFS.html>`__ to enforce pod CPU limits. When the node runs many CPU-bound pods, the workload can move to different CPU cores depending on whether the pod is throttled and which CPU cores are available at scheduling time. Many workloads are not sensitive to this migration and thus work fine without any intervention. Some applications are CPU-sensitive. They are sensitive to:

-  CPU throttling
-  Context switching
-  Processor cache misses
-  Cross-socket memory access
-  Hyperthreads that are expected to run on the same physical CPU card

If your workloads are sensitive to any of these items and CPU cache affinity and scheduling latency significantly affect workload performance, kubelet allows alternative CPU management policies to determine some placement preferences on the node. The CPU manager preferentially allocates resources on a socket and full physical cores to avoid interference.

Binding CPU Cores to a Pod
--------------------------

Prerequisites:

-  The static core binding policy is enabled on the node. For details, see :ref:`Enabling the CPU Management Policy <cce_10_0351__section173918176434>`.
-  Both **requests** and **limits** must be set in the pod definition and their values must be the same.
-  The value of **requests** must be an integer for the container.
-  For an init container, it is recommended that you set its **requests** to the same as that of the service container. Otherwise, the service container does not inherit the CPU allocation result of the init container, and the CPU manager reserves more CPU resources than supposed. For more information, see `App Containers can't inherit Init Containers CPUs - CPU Manager Static Policy <https://github.com/kubernetes/kubernetes/issues/94220#issuecomment-868489201>`__.

You can use :ref:`Scheduling Policy (Affinity/Anti-affinity) <cce_10_0232>` to schedule the configured pods to the nodes where the static CPU policy is enabled. In this way, cores can be bound.

.. _cce_10_0351__section173918176434:

Enabling the CPU Management Policy
----------------------------------

A `CPU management policy <https://kubernetes.io/docs/tasks/administer-cluster/cpu-management-policies/>`__ is specified by the kubelet flag **--cpu-manager-policy**. The following policies are supported:

-  Disabled (**none**): the default policy. The **none** policy explicitly enables the existing default CPU affinity scheme, providing no affinity beyond what the OS scheduler does automatically.
-  Enabled (**static**): The **static** policy allows containers in **Guaranteed** pods with integer CPU requests to be granted increased CPU affinity and exclusivity on the node.

When creating a cluster, you can configure the CPU management policy in **Advanced Settings**, as shown in the following figure.

|image1|

You can also configure the policy in a node pool. The configuration will change the kubelet flag **--cpu-manager-policy** on the node. Log in to the CCE console, click the cluster name, access the cluster details page, and choose **Nodes** in the navigation pane. On the page displayed, click the **Node Pools** tab. Choose **More** > **Manage** in the **Operation** column of the target node pool, and change the value of **cpu-manager-policy** to **static**.

Pod Configuration
-----------------

For CPU, both **requests** and **limits** must be set to the same, and **requests** must be an integer.

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

.. |image1| image:: /_static/images/en-us_image_0000001569022837.png
