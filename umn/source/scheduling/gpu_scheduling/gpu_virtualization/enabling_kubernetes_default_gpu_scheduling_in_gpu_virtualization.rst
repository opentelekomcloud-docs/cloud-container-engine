:original_name: cce_10_0742.html

.. _cce_10_0742:

Enabling Kubernetes' Default GPU Scheduling in GPU Virtualization
=================================================================

With GPU virtualization enabled, workloads can still use **nvidia.com/gpu** (:ref:`Kubernetes' default GPU scheduling <cce_10_0345>`) while optionally adding fine-grained isolation via **volcano.sh/gpu-mem.128Mi** (memory only) or both **volcano.sh/gpu-mem.128Mi** and **volcano.sh/gpu-core.percentage** (both compute and memory).

With Kubernetes' default GPU scheduling and virtualization enabled, **nvidia.com/gpu** can be expressed as either a fraction or an integer.

-  When **nvidia.com/gpu** is set to a fraction:

   -  If workload pods are scheduled to a node that does not support GPU virtualization (for details, see :ref:`Prerequisites <cce_10_0644__section911495392713>`), all pods requesting fractions simply share the entire physical GPU, including its compute and memory resources. This is suitable for scenarios with low compute demands, such as lightweight inference tasks.
   -  If workload pods are scheduled to a node that supports GPU virtualization (for details, see :ref:`Prerequisites <cce_10_0644__section911495392713>`), pods run in memory-isolation mode and can coexist only with other memory-isolated pods. They cannot share the GPU with pods that also isolate compute. GPU memory is allocated in 128-MiB blocks, rounded down to the nearest multiple of 128 MiB based on the requested **nvidia.com/gpu** ratio. For example, on a 16-GiB GPU, if **nvidia.com/gpu** is set to **0.5**, the containers will be allocated 8 GiB of GPU memory (0.5 x 16 GiB), or 8192 MiB (64 x 128 MiB).

-  If **nvidia.com/gpu** is set to an integer, an entire physical GPU will be exclusively allocated to the target pod. This is ideal for scenarios requiring high performance and strict isolation.

.. note::

   Pods requesting GPUs under different resource names cannot share a node. For example, the pods requesting **nvidia.com/gpu** resources and the pods requesting **volcano.sh/gpu-mem.128Mi** resources cannot be co-located on the same node. The scheduler treats them as distinct resource types and will not bin-pack them.

Notes and Constraints
---------------------

To support Kubernetes' default GPU scheduling on GPU nodes, the CCE AI Suite (NVIDIA GPU) add-on must be of v2.0.10 or later, and the Volcano Scheduler add-on must be of v1.10.5 or later.

Example of Shared GPU Scheduling
--------------------------------

#. Use kubectl to access the cluster.

#. Create a workload that uses nvidia.com/gpu resources.

   Create a **gpu-app.yaml** file. The following is an example:

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
          spec:
            schedulerName: volcano
            containers:
              image: <your_image_address>     # Replace it with your image address.
              name: container-0
              resources:
                requests:
                  cpu: 250m
                  memory: 512Mi
                  nvidia.com/gpu: 0.1   # The value must be the same as that of limits.nvidia.com/gpu.
                limits:
                  cpu: 250m
                  memory: 512Mi
                  nvidia.com/gpu: 0.1   # The requested GPUs
            imagePullSecrets:
            - name: default-secret

#. Run the following command to create an application:

   .. code-block::

      kubectl apply -f gpu-app.yaml

#. Log in to the pod and check the total GPU memory allocated to the pod.

   .. code-block::

      kubectl exec -it gpu-app -- nvidia-smi

   Expected output:

   .. code-block::

      Thu Jul 27 07:53:49 2023
      +-----------------------------------------------------------------------------+
      | NVIDIA-SMI 470.57.02    Driver Version: 470.57.02    CUDA Version: 11.4     |
      |-------------------------------+----------------------+----------------------+
      | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
      | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
      |                               |                      |               MIG M. |
      |===============================+======================+======================|
      |   0  NVIDIA A30          Off  | 00000000:00:0D.0 Off |                    0 |
      | N/A   47C    P0    34W / 165W |      0MiB /  2304MiB |      0%      Default |
      |                               |                      |             Disabled |
      +-------------------------------+----------------------+----------------------+

      +-----------------------------------------------------------------------------+
      | Processes:                                                                  |
      |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
      |        ID   ID                                                   Usage      |
      |=============================================================================|
      |  No running processes found                                                 |
      +-----------------------------------------------------------------------------+

   The output shows that the total GPU memory that can be used by the pod is 2304 MiB.

   In this example, the node offers 24258 MiB of GPU memory in total. A 0.1-GPU request (2425.8 MiB) is rounded down to the nearest 128 MiB block, yielding 2304 MiB (18 x 128 MiB).
