:original_name: cce_10_0646.html

.. _cce_10_0646:

Using GPU Virtualization
========================

This section describes how to use the GPU virtualization capability to isolate the computing power from the GPU memory and efficiently use GPU device resources.

Prerequisites
-------------

-  You have :ref:`prepared GPU virtualization resources <cce_10_0645>`.
-  To create a cluster using commands, ensure kubectl is used. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

Notes and Constraints
---------------------

-  A single GPU can be virtualized into a maximum of 20 virtual GPU devices.
-  Virtual GPUs cannot be used in init containers.
-  GPU virtualization supports GPU memory isolation and isolation between GPU memory and computing power. A single GPU can schedule only workloads in the same isolation mode.
-  Autoscaler does not support automatic scaling of virtual GPU nodes in clusters of v1.26 or earlier.
-  When isolation is required, virtual GPUs do not support requesting GPU memory using the CUDA API cudaMallocManaged(), which is also known as Unified Virtual Memory (UVM). For more information, see `NVIDIA official documents <https://developer.nvidia.com/blog/unified-memory-cuda-beginners/>`__. Use other methods to request for GPU memory, for example, by calling cudaMalloc().
-  When a containerized application is initializing, the real-time compute monitored by the nvidia-smi may exceed the upper limit of the available compute of the container.
-  Due to GPU virtualization limitations, compute and GPU memory isolation backed by GPU virtualization is not applicable in rendering scenarios. For rendering, use either whole GPUs or GPU memory isolation.
-  When GPU virtualization is enabled on a node with multiple GPUs, insufficient GPU resources will not result in the preempting of GPU resources from other pods.

Creating a GPU Virtualization Application
-----------------------------------------

**Using the CCE Console**

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Workloads**. In the upper right corner, click **Create Workload**.

#. Configure basic information of the workload.

   In **Basic Info** under **Container Settings**, configure the GPU quota.

   -  **GPU memory**: The unit is MiB. The value must be a positive integer that is a multiple of 128. If the value exceeds the memory of a single GPU, scheduling cannot be performed.
   -  **Computing Power**: The unit is %. The value must be a multiple of 5 and cannot exceed 100.

   .. note::

      -  If the GPU memory is set to the capacity upper limit of a single GPU or the computing power is set to 100%, the entire GPU will be used.
      -  When GPU virtualization is used, the workload scheduler defaults to Volcano and cannot be changed.

   This section describes how to use GPU virtualization. For details about other parameters, see :ref:`Workloads <cce_10_0046>`.

   After the configuration, click **Create Workload**.

#. After a workload is created, you can try to verify the isolation capability of GPU virtualization.

   a. Log in to the pod and check the total GPU memory allocated to the pod.

      .. code-block::

         kubectl exec -it gpu-app -- nvidia-smi

      Expected output:

      .. code-block::

         Wed Apr 12 07:54:59 2023
         +-----------------------------------------------------------------------------+
         | NVIDIA-SMI 470.57.02   Driver Version: 470.57.02   CUDA Version: 11.4     |
         |-------------------------------+----------------------+----------------------+
         | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
         | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
         |                               |                      |               MIG M. |
         |===============================+======================+======================|
         |   0  Tesla V100-SXM2...  Off  | 00000000:21:01.0 Off |                    0 |
         | N/A   27C    P0    37W / 300W |   0MiB /  5120MiB |      0%      Default |
         |                               |                      |                  N/A |
         +-------------------------------+----------------------+----------------------+

         +-----------------------------------------------------------------------------+
         | Processes:                                                                  |
         |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
         |        ID   ID                                                   Usage      |
         |=============================================================================|
         +-----------------------------------------------------------------------------+

      The pod does not use GPU resources. Therefore, although the total GPU memory allocated to the container is 5120 MiB, the actual usage is 0 MiB. After GPU programs are deployed in the pod, the actual GPU usage will no longer be 0 MiB.

   b. Run the following command on the node to check the isolation status of the GPU memory:

      .. code-block::

         nvidia-smi

      Expected output:

      .. code-block::

         Wed Apr 12 09:31:10 2023
         +-----------------------------------------------------------------------------+
         | NVIDIA-SMI 470.57.02   Driver Version: 470.57.02   CUDA Version: 11.4     |
         |-------------------------------+----------------------+----------------------+
         | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
         | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
         |                               |                      |               MIG M. |
         |===============================+======================+======================|
         |   0  Tesla V100-SXM2...  Off  | 00000000:21:01.0 Off |                    0 |
         | N/A   27C    P0    37W / 300W |   0MiB / 16160MiB |      0%      Default |
         |                               |                      |                  N/A |
         +-------------------------------+----------------------+----------------------+

         +-----------------------------------------------------------------------------+
         | Processes:                                                                  |
         |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
         |        ID   ID                                                   Usage      |
         |=============================================================================|
         |  No running processes found                                                 |
         +-----------------------------------------------------------------------------+

      The expected output indicates that the total GPU memory on the GPU node is 16160 MiB. Since the sample pod does not run any GPU program, the used GPU memory is 0 MiB.

**Using kubectl**

#. Use kubectl to access the cluster.

#. Create an application that uses GPU virtualization.

   .. note::

      The GPU memory isolation and isolation between GPU memory and computing power are supported. The computing power cannot be isolated only. **volcano.sh** and **gpu-core.percentage** cannot be set separately.

   Create a **gpu-app.yaml** file. The following is an example:

   -  Isolate GPU memory only:

      .. code-block::

         apiVersion: apps/v1
         kind: Deployment
         metadata:
           name: gpu-app
           labels:
             app: gpu-app
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
               containers:
               - name: container-1
                 image: <your_image_address>     # Replace it with your image address.
                 resources:
                   limits:
                     volcano.sh/gpu-mem.128Mi: 40  # The GPU memory allocated to the pod. This value represents 5120 MiB (40 x 128 MiB).
               imagePullSecrets:
                 - name: default-secret
               schedulerName: volcano

   -  Isolate the GPU memory from computing power:

      .. code-block::

         apiVersion: apps/v1
         kind: Deployment
         metadata:
           name: gpu-app
           labels:
             app: gpu-app
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
               containers:
               - name: container-1
                 image: <your_image_address>     # Replace it with your image address.
                 resources:
                   limits:
                     volcano.sh/gpu-mem.128Mi: 40  # The GPU memory allocated to the pod. This value represents 5120 MiB (40 x 128 MiB).
                     volcano.sh/gpu-core.percentage: 25    # The compute power allocated to the pod
               imagePullSecrets:
                 - name: default-secret
               schedulerName: volcano

   .. table:: **Table 1** Key parameters

      +--------------------------------+-----------+----------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                      | Mandatory | Description                                                                                                                                                    |
      +================================+===========+================================================================================================================================================================+
      | volcano.sh/gpu-mem.128Mi       | No        | The value is a positive integer that is a multiple of 128 in the unit of MiB. If the value exceeds the memory of a single GPU, scheduling cannot be performed. |
      +--------------------------------+-----------+----------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | volcano.sh/gpu-core.percentage | No        | The unit of the computing power is %. The value must be a multiple of 5 and cannot exceed 100.                                                                 |
      +--------------------------------+-----------+----------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. note::

      -  If the GPU memory is set to the capacity upper limit of a single GPU or the computing power is set to 100%, the entire GPU will be used.
      -  When GPU virtualization is used, the workload scheduler defaults to Volcano and cannot be changed.

#. Run the following command to create an application:

   .. code-block::

      kubectl apply -f gpu-app.yaml

#. Verify the isolation capability of GPU virtualization.

   a. Log in to the pod and check the total GPU memory allocated to the pod.

      .. code-block::

         kubectl exec -it gpu-app -- nvidia-smi

      Expected output:

      .. code-block::

         Wed Apr 12 07:54:59 2023
         +-----------------------------------------------------------------------------+
         | NVIDIA-SMI 470.57.02   Driver Version: 470.57.02   CUDA Version: 11.4     |
         |-------------------------------+----------------------+----------------------+
         | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
         | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
         |                               |                      |               MIG M. |
         |===============================+======================+======================|
         |   0  Tesla V100-SXM2...  Off  | 00000000:21:01.0 Off |                    0 |
         | N/A   27C    P0    37W / 300W |   0MiB /  5120MiB |      0%      Default |
         |                               |                      |                  N/A |
         +-------------------------------+----------------------+----------------------+

         +-----------------------------------------------------------------------------+
         | Processes:                                                                  |
         |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
         |        ID   ID                                                   Usage      |
         |=============================================================================|
         +-----------------------------------------------------------------------------+

      The pod does not use GPU resources. Therefore, although the total GPU memory allocated to the container is 5120 MiB, the actual usage is 0 MiB. After GPU programs are deployed in the pod, the actual GPU usage will no longer be 0 MiB.

   b. Run the following command on the node to check the isolation status of the GPU memory:

      .. code-block::

         nvidia-smi

      Expected output:

      .. code-block::

         Wed Apr 12 09:31:10 2023
         +-----------------------------------------------------------------------------+
         | NVIDIA-SMI 470.57.02   Driver Version: 470.57.02   CUDA Version: 11.4     |
         |-------------------------------+----------------------+----------------------+
         | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
         | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
         |                               |                      |               MIG M. |
         |===============================+======================+======================|
         |   0  Tesla V100-SXM2...  Off  | 00000000:21:01.0 Off |                    0 |
         | N/A   27C    P0    37W / 300W |   0MiB / 16160MiB |      0%      Default |
         |                               |                      |                  N/A |
         +-------------------------------+----------------------+----------------------+

         +-----------------------------------------------------------------------------+
         | Processes:                                                                  |
         |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
         |        ID   ID                                                   Usage      |
         |=============================================================================|
         |  No running processes found                                                 |
         +-----------------------------------------------------------------------------+

      The expected output indicates that the total GPU memory on the GPU node is 16160 MiB. Since the sample pod does not run any GPU program, the used GPU memory is 0 MiB.
