:original_name: cce_10_0846.html

.. _cce_10_0846:

Selecting a GPU Driver Version for Nodes
========================================

Before using GPU-accelerated ECSs, install the necessary NVIDIA infrastructure software to enable accelerated GPU computing. To use GPUs, select and install the appropriate software package that matches the GPU model.

This section describes how to select a driver version and CUDA Toolkit for GPU nodes.


Selecting a GPU Driver Version for Nodes
----------------------------------------

To use GPU resources, you typically need to install the following software packages of the desired version:

-  Hardware driver for GPUs, such as a Tesla driver
-  Libraries required by upper-layer applications, such as CUDA Toolkit

When using container applications, a node will have a GPU driver installed, and the CUDA Toolkit will be pre-installed during the building of an application's container image. Alternatively, you can use a `NVIDIA base image <https://hub.docker.com/r/nvidia/cuda>`__ that already has the CUDA Toolkit pre-installed to build the application's container image. To use GPU resources, the GPU driver version must match the CUDA Toolkit version.

You can run the **nvidia-smi** command to obtain the driver installed on the node and determine the mapping between the NVIDIA driver and CUDA Toolkit version. In the figure below, the driver version is 470.141.03, and the latest supported CUDA Toolkit version is 11.4.


.. figure:: /_static/images/en-us_image_0000002467719741.png
   :alt: **Figure 1** Mapping between the NVIDIA driver and CUDA Toolkit version

   **Figure 1** Mapping between the NVIDIA driver and CUDA Toolkit version

Compatibility Between CUDA Toolkit and Driver Versions
------------------------------------------------------

When selecting a NVIDIA driver, ensure that the driver version is compatible with the CUDA Toolkit version. The following table lists the `mapping <https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html#id5>`__. The table below shows the earliest driver versions that are compatible with the CUDA Toolkit. For more detailed version mapping, see `CUDA Toolkit and Corresponding Driver Versions <https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html#id6>`__. You can select a proper NVIDIA driver version based on the CUDA Toolkit version used by your application.

.. note::

   If the CUDA and driver versions listed in the table below match, preferentially use the GPU driver provided in :ref:`Recommended GPU Driver Versions for CCE <cce_10_0847>`. If the driver version recommended by CCE does not match your CUDA Toolkit version, you will need to use a non-recommended driver version. In such cases, ensure compatibility between the model, OS, and driver version.

+-----------------------------------+------------------------------------------------+
| CUDA Toolkit Version              | Minimum Driver Version Required (Linux x86_64) |
+===================================+================================================+
| CUDA 12.x                         | >= 525.60.13                                   |
+-----------------------------------+------------------------------------------------+
| CUDA 11.8.x                       | >= 450.80.02                                   |
|                                   |                                                |
| CUDA 11.7.x                       |                                                |
|                                   |                                                |
| CUDA 11.6.x                       |                                                |
|                                   |                                                |
| CUDA 11.5.x                       |                                                |
|                                   |                                                |
| CUDA 11.4.x                       |                                                |
|                                   |                                                |
| CUDA 11.3.x                       |                                                |
|                                   |                                                |
| CUDA 11.2.x                       |                                                |
|                                   |                                                |
| CUDA 11.1.x                       |                                                |
+-----------------------------------+------------------------------------------------+
| CUDA 11.0                         | >= 450.36.06                                   |
+-----------------------------------+------------------------------------------------+
