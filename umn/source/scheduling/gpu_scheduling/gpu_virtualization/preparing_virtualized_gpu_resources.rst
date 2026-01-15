:original_name: cce_10_0645.html

.. _cce_10_0645:

Preparing Virtualized GPU Resources
===================================

CCE uses xGPU virtualization technologies to dynamically divide the GPU memory and computing power. A single GPU can be virtualized into a maximum of 20 virtual GPU devices. This section describes how to implement GPU scheduling and isolation capabilities on GPU nodes.

.. _cce_10_0645__section14347758125812:

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

Step 1: Enable GPU Virtualization
---------------------------------

Both :ref:`CCE AI Suite (NVIDIA GPU) <cce_10_0141>` and :ref:`Volcano Scheduler <cce_10_0193>` must be installed in the cluster.

#. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Settings**.

#. Switch to the **Heterogeneous Resources** tab and enable **GPU Virtualization**.

   -  **Node pool-level GPU virtualization:** If CCE AI Suite (NVIDIA GPU) of version 2.7.2 or later is installed, GPU virtualization can be configured by node pool.

      a. In **Node Pool Configurations** under **GPU Settings**, click **Add**.
      b. In the **Node Pools** list, select the node pool where you want to enable GPU virtualization and choose a driver that supports GPU virtualization from **Driver**. After you customize a GPU driver for a node pool, nodes in that pool will preferentially use the custom driver. Nodes for which no driver is specified will use the cluster's default driver.

         .. note::

            -  The specified driver version will be installed for new nodes added to the pool. This configuration does not affect existing nodes in the pool.
            -  An updated driver version applies only to new nodes added to the node pool. Existing nodes must be restarted to apply the changes.

      c. Click |image1| under **GPU Virtualization** to enable GPU virtualization for the node pool. To configure GPU virtualization for multiple node pools, click **Add**.
      d. In the lower right corner of the page, click **Confirm Settings**.

#. After configuring GPU virtualization, verify the settings.

   In the navigation pane, choose **Cluster** > **Nodes**. In the right pane, click the **Nodes** tab and find the node where GPU virtualization has been configured. In the **Operation** column of the target node, choose **More > View YAML**. If the **node-status.volcano.sh/nvidia** value in the YAML file is **{"enableXGPU":true}**, GPU virtualization has been configured on the node.

Step 2: Create a GPU Node
-------------------------

Create nodes that support GPU virtualization in the cluster to use the GPU virtualization function. For details, see :ref:`Creating a Node <cce_10_0363>` or :ref:`Creating a Node Pool <cce_10_0012>`. If there are GPU nodes in your cluster that meet the :ref:`prerequisites <cce_10_0645__section14347758125812>` requirements, skip this step.

.. |image1| image:: /_static/images/en-us_image_0000002483959290.png
