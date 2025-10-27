:original_name: cce_10_0788.html

.. _cce_10_0788:

Heterogeneous Resources
=======================

GPU Settings
------------

-  **Default Cluster Driver**: specifies the default GPU driver version used by the GPU nodes in a cluster. To use a custom driver, enter the download link of the NVIDIA driver. For details, see :ref:`Obtaining a Driver Link from the Internet <cce_10_0141__section95451728192112>`.
-  **Node Pool Configurations**: If you do not want all GPU nodes in a cluster to use the same driver, CCE allows you to install a different GPU driver for each node pool. After you customize a GPU driver for a node pool, nodes in the node pool will preferentially use the custom driver. Nodes for which no driver is specified will use the cluster's default driver.

   .. note::

      -  The system installs the driver of the version specified for the node pool. The driver applies only to new nodes in the node pool.
      -  After the driver version is updated, it takes effect on the nodes newly added to the node pool. Existing nodes must restart to apply the changes.
