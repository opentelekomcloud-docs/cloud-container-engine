:original_name: cce_10_0660.html

.. _cce_10_0660:

Upgrading an OS
===============

When CCE releases a new OS image, existing nodes cannot be automatically upgraded. You can manually upgrade them in batches.

.. important::

   This section describes how to upgrade an OS by resetting the target node. **Workloads running on a node may be interrupted due to standalone deployment or insufficient schedulable resources.** Evaluate the upgrade risks and perform the upgrade during off-peak hours. Alternatively, `specify a disruption budget for your key applications <https://kubernetes.io/docs/tasks/run-application/configure-pdb/>`__ to ensure the availability of these applications during the upgrade.

Constraints
-----------

-  Nodes running private images cannot be upgraded.
-  Compatibility issues may occur when the node OS of an early version is upgraded. In this case, manually reset the node for the OS upgrade.

Procedure for Default Node Pools
--------------------------------

#. Log in to the CCE console.
#. Click the cluster name to access the cluster console. Choose **Nodes** in the navigation pane and click the **Node Pools** tab on the right.
#. Click **Upgrade** next to the default node pool.
#. In the displayed **Operating System Upgrade** window, configure parameters.

   -  **Target Operating System**: shows the image of the target version. You do not need to configure this parameter.

   -  **Upgrade Policy**: **Node Reset** is supported.

   -  **Max. Nodes for Batch Upgrade**: maximum number of nodes that will be unavailable during node upgrade. Nodes will be unavailable during synchronization by resetting the nodes. Properly configure this parameter to prevent pod scheduling failures caused by too many unavailable nodes in the cluster.

   -  **View Node**: Select the nodes to be upgraded.

   -  Login Mode:

      -  **Key Pair**

         Select the key pair used to log in to the node. You can select a shared key.

         A key pair is used for identity authentication when you remotely log in to a node. If no key pair is available, click **Create Key Pair**.

   -  Pre-installation script:

      Pre-installation script command, in which Chinese characters are not allowed. The script command will be Base64-transcoded.

      The script will be executed before Kubernetes software is installed. Note that if the script is incorrect, Kubernetes software may fail to be installed.

   -  Post-installation script:

      Pre-installation script command, in which Chinese characters are not allowed. The script command will be Base64-transcoded.

      The script will be executed after Kubernetes software is installed and will not affect the installation.

#. Click **OK**.

Procedure for Non-default Node Pools
------------------------------------

#. Log in to the CCE console.
#. Click the cluster name to access the cluster console. Choose **Nodes** in the navigation pane and click the **Node Pools** tab on the right.
#. Choose **More > Synchronize** in the **Operation** column of the target node pool.
#. In the **Batch synchronization** window, configure parameters.

   -  **OS**: shows the image of the target version. You do not need to configure this parameter.
   -  **Synchronization Policy**: **Node Reset** is supported.
   -  **Max. Nodes for Batch Synchronize**: maximum number of nodes that will be unavailable during node synchronization. Nodes will be unavailable during synchronization by resetting the nodes. Properly configure this parameter to prevent pod scheduling failures caused by too many unavailable nodes in the cluster.
   -  **Node List**: Select the nodes requiring the synchronization of node pool configurations.

#. Click **OK**.
