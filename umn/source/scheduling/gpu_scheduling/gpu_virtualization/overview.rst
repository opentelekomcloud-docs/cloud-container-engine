:original_name: cce_10_0644.html

.. _cce_10_0644:

Overview
========

CCE uses xGPU virtualization technologies to dynamically divide the GPU memory and computing power. A single GPU can be virtualized into a maximum of 20 virtual GPU devices. Virtualization is more flexible than static allocation. You can specify the number of GPUs on the basis of stable service running to improve GPU utilization.

Advantages
----------

The GPU virtualization function of CCE has the following advantages:

-  **Flexible**: The GPU computing ratio and memory size are finely tuned. The allocation granularity for computing is 5% of GPUs, while the memory allocation is in MiB.
-  **Isolated**: The memory of a single GPU can be isolated, and both GPU computing and memory can be isolated concurrently.
-  **Compatible**: There is no need to recompile services or replace the CUDA library.

.. _cce_10_0644__section911495392713:

Prerequisites
-------------

+-----------------------------------+-------------------------------------------------------------------+
| Item                              | Supported Version                                                 |
+===================================+===================================================================+
| Cluster version                   | v1.23.8-r0, v1.25.3-r0, or later                                  |
+-----------------------------------+-------------------------------------------------------------------+
| OS                                | HCE OS 2.0 with the kernel version of 5.10 or later               |
+-----------------------------------+-------------------------------------------------------------------+
| GPU type                          | Tesla T4 and Tesla V100                                           |
+-----------------------------------+-------------------------------------------------------------------+
| Driver version                    | 570.86.15, 535.216.03, 535.54.03, 510.47.03, and 470.57.02        |
+-----------------------------------+-------------------------------------------------------------------+
| CUDA version                      | CUDA 12.2.0 to 12.8.0                                             |
+-----------------------------------+-------------------------------------------------------------------+
| Runtime                           | containerd                                                        |
+-----------------------------------+-------------------------------------------------------------------+
| Add-on                            | The following add-ons must be installed in the cluster:           |
|                                   |                                                                   |
|                                   | -  :ref:`Volcano Scheduler <cce_10_0193>`: 1.10.5 or later        |
|                                   | -  :ref:`CCE AI Suite (NVIDIA GPU) <cce_10_0141>`: 2.0.5 or later |
+-----------------------------------+-------------------------------------------------------------------+

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

Process of GPU Virtualization
-----------------------------

-  :ref:`Preparing Virtualized GPU Resources <cce_10_0645>`: Prepare nodes that support GPU virtualization.
-  :ref:`Using GPU Virtualization <cce_10_0646>`: Create a GPU virtualization workload to meet service needs.
