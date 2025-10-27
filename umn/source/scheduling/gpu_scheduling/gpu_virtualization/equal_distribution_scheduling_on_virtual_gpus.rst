:original_name: cce_10_0965.html

.. _cce_10_0965:

Equal Distribution Scheduling on Virtual GPUs
=============================================

In AI training, inference, and scientific computing, a single GPU often falls short due to limited compute power or memory. Multiple GPUs are therefore needed to work together. However, using entire GPU cards for collaboration can lead to resource wastage, especially when tasks require only a portion of the GPU's memory or compute power. Equal distribution scheduling on virtual GPUs addresses this issue by efficiently utilizing GPU memory and compute power across multiple cards, optimizing resource use.

This policy equally allocates requested GPU resources (such as GPU memory and compute power) across multiple virtual GPUs, improving utilization and reducing waste. With this approach, a pod can flexibly use multiple virtual GPUs, with each providing an equal number of resources. This enables refined allocation and efficient utilization of GPU resources. Equal distribution scheduling on virtual GPUs supports GPU memory isolation by configuring **volcano.sh/gpu-mem.128Mi** and isolation of both compute and memory resources by configuring both **volcano.sh/gpu-mem.128Mi** and **volcano.sh/gpu-core.percentage**.

-  **GPU memory isolation**: Tasks can split their requested GPU memory across multiple cards for sharing. For example, if an application requests M MiB of GPU memory and specifies to use N GPU cards on a single node, CCE will equally allocate the M MiB of memory across the N cards. During execution, each task is limited to using M/N MiB of memory per GPU card, ensuring memory isolation between tasks and preventing resource contention.
-  **Isolation of both compute and memory resources**: Tasks can split their requested GPU memory and compute power across multiple cards for sharing. For example, if an application requests M MiB of GPU memory and T% of compute power and specifies to use N GPU cards on a single node, CCE will equally allocate the M MiB of memory and T% of compute power across the N cards. During execution, each task is limited to using M/N MiB of memory and T/N% of compute power per GPU card.

.. note::

   In GPU virtualization, the allocated GPU memory in MiB must be an integer multiple of 128. Therefore, M/N, the GPU memory per card, must meet this requirement. Additionally, the compute power in percentage must be an integer multiple of 5. Therefore, T/N, the compute power per card, must be a multiple of 5.

Prerequisites
-------------

-  A CCE standard or Turbo cluster of v1.27.16-r20, v1.28.15-r10, v1.29.10-r10, v1.30.6-r10, v1.31.4-r0, or later is available.
-  CCE AI Suite (NVIDIA GPU) has been installed in the cluster. For details, see :ref:`CCE AI Suite (NVIDIA GPU) <cce_10_0141>`. The add-on version must meet the following requirements:

   -  If the cluster version is 1.27 or earlier, the add-on version must be 2.1.41 or later.
   -  If the cluster version is 1.28 or later, the add-on version must be 2.7.57 or later.

-  GPU nodes with virtualization enabled at the node pool level are available in the cluster.
-  Volcano of v1.16.10 or later has been installed.

Notes and Constraints
---------------------

-  Equal distribution scheduling on virtual GPUs is not compatible with Kubernetes' default GPU scheduling (which involves workloads using nvidia.com/gpu resources).
-  Node pool auto scaling is not supported in equal distribution scheduling on virtual GPUs scenarios. If node pool auto scaling is enabled, scale-out behavior may become unpredictable.

Using the Console
-----------------

#. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Workloads**. In the upper right corner of the displayed page, click **Create Workload**.

#. In the **Container Settings** area, choose **Basic Info** in the navigation pane and then set **GPU Quota** to **GPU Virtualization**. Enable **Multi-card Equal Distribution Scheduling**. For details about parameter settings, see :ref:`Table 1 <cce_10_0965__table4987121825419>`.

   **The settings of multi-card equal distribution scheduling and the number of GPUs apply cluster-wide. Any modifications made will affect all containers in the cluster.**

   .. _cce_10_0965__table4987121825419:

   .. table:: **Table 1** Parameters for multi-card equal distribution scheduling

      +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter             | Example Value         | Description                                                                                                                                                                       |
      +=======================+=======================+===================================================================================================================================================================================+
      | GPUs                  | 1                     | (Mandatory) The number of GPUs for multi-card equal distribution scheduling.                                                                                                      |
      +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Total GPU Memory      | 128                   | (Mandatory) The requested GPU memory size in MiB. It must be a positive integer and a multiple of 128. If the specified value exceeds the total GPU memory, scheduling will fail. |
      +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Total Computing       | 5                     | (Optional) The requested GPU computing as a percentage. The value must be a multiple of 5 and is determined by the number of GPUs.                                                |
      |                       |                       |                                                                                                                                                                                   |
      |                       |                       | -  If this parameter is left blank, the container uses GPU memory isolation.                                                                                                      |
      |                       |                       | -  If set, the container uses isolation of both compute and memory resources.                                                                                                     |
      +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Configure other parameters by referring to :ref:`Creating a Workload <cce_10_0673>`. Then, click **Create Workload** in the lower right corner. When the status changes to **Running**, the workload has been created.

Using kubectl
-------------

The following example illustrates how to create a workload that uses equal distribution scheduling on virtual GPUs with GPU memory isolation: Configure this workload as follows: Set the number of pods to **1**, the requested GPU memory to **8 GiB**, and specify the workload to use two GPUs. Each GPU will allocate 4 GiB of memory. After the workload is created, CCE will automatically schedule it to a GPU node that meets these conditions.

#. Use kubectl to access the cluster.

#. Run the following command to create a YAML file for a workload that requires equal distribution scheduling on virtual GPUs:

   .. code-block::

      vim gpu-app.yaml

   Example file content:

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: gpu-app
        namespace: default
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: gpu-app
        template:
          metadata:
            labels:
              app: gpu-app
              volcano.sh/gpu-num: '2'    # Number of GPUs for equal distribution scheduling. In this example, the pod requests two GPUs, with each GPU providing 4 GiB of memory.
          spec:
            schedulerName: volcano
            containers:
            - image: <your_image_address>     # Replace it with your image address.
              name: container-0
              resources:
                requests:
                  cpu: 250m
                  memory: 512Mi
                  volcano.sh/gpu-mem.128Mi: '64' # Requested GPU memory. The value 64 indicates that 8 GiB of GPU memory is requested (64 x 128 MiB/1024).
                limits:
                  cpu: 250m
                  memory: 512Mi
                  volcano.sh/gpu-mem.128Mi: '64' # Upper limit of the GPU memory that can be used, which is 8 GiB
            imagePullSecrets:
            - name: default-secret

   To enable isolation of both compute and memory resources, configure **volcano.sh/gpu-core.percentage** in both **resources.requests** and **resources.limits** to allocate GPU compute power to pods, for example, set **volcano.sh/gpu-core.percentage** to **5**.

#. Run the following command to create the workload:

   .. code-block::

      kubectl apply -f gpu-app.yaml

   If information similar to the following is displayed, the workload has been created:

   .. code-block::

      deployment.apps/gpu-app created

#. Run the following command to view the created pod:

   .. code-block::

      kubectl get pod -n default

   Information similar to the following is displayed:

   .. code-block::

      NAME                      READY   STATUS    RESTARTS   AGE
      gpu-app-6bdb4d7cb-pmtc2   1/1     Running   0          21s

#. Log in to the pod and check the total GPU memory allocated to the pod.

   .. code-block::

      kubectl exec -it gpu-app-6bdb4d7cb-pmtc2 -- nvidia-smi

   Expected output:

   .. code-block::

      Fri Mar  7 03:36:03 2025
      +---------------------------------------------------------------------------------------+
      | NVIDIA-SMI 535.216.03             Driver Version: 535.216.03   CUDA Version: 12.2     |
      |-----------------------------------------+----------------------+----------------------+
      | GPU  Name                 Persistence-M | Bus-Id        Disp.A | Volatile Uncorr. ECC |
      | Fan  Temp   Perf          Pwr:Usage/Cap |         Memory-Usage | GPU-Util  Compute M. |
      |                                         |                      |               MIG M. |
      |=========================================+======================+======================|
      |   0  Tesla T4                       Off | 00000000:00:0D.0 Off |                    0 |
      | N/A   33C    P8               9W /  70W |      0MiB /  4096MiB |      0%      Default |
      |                                         |                      |                  N/A |
      +-----------------------------------------+----------------------+----------------------+
      |   1  Tesla T4                       Off | 00000000:00:0E.0 Off |                    0 |
      | N/A   34C    P8               9W /  70W |      0MiB /  4096MiB |      0%      Default |
      |                                         |                      |                  N/A |
      +-----------------------------------------+----------------------+----------------------+
      +---------------------------------------------------------------------------------------+
      | Processes:                                                                            |
      |  GPU   GI   CI        PID   Type   Process name                            GPU Memory |
      |        ID   ID                                                             Usage      |
      |=======================================================================================|
      |  No running processes found                                                           |
      +---------------------------------------------------------------------------------------+

   The command output shows that the pod can use two GPUs, each providing 4 GiB of GPU memory (4096 MiB/1024). The pod's requested GPU memory has been evenly distributed across the two GPUs, with each GPU's memory resources isolated.

Additional Information
----------------------

When GPU memory or compute is allocated to xGPU services, CCE applies bin packing by default to distribute workloads efficiently across multiple GPUs on a node. This helps reduce resource fragmentation and improves overall utilization in the cluster. However, when an equal distribution scheduling job is delivered, it may still enter a pending state despite sufficient total idle resources on the node. This happens because the number of available GPUs may not meet the scheduling requirement. That is an expected behavior.

For example, a node has three GPUs (numbered 0, 1, and 2) with remaining memory of 4 GiB, 4 GiB, and 8 GiB, respectively. There is a workload with two pods, each pod requesting two GPUs and 4 GiB of memory per GPU. The total GPU memory available on the node is 16 GiB (4 + 4 + 8), exactly what the workload demands (2 pods x 2 x 4 = 16 GiB). Following bin packing rules, CCE assigns GPU 0 and GPU 1 to the first pod. This satisfies its requirements of two separate GPUs and 4 GiB of memory. There is only one GPU left, GPU 2 with 8 GiB of memory. The second pod still needs two separate GPUs, but the node no longer meets that condition. So even though there is enough memory, the second pod fails to schedule, and the workload cannot be placed on the node.
