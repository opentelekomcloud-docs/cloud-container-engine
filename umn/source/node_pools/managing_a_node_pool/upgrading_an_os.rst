:original_name: cce_10_0660.html

.. _cce_10_0660:

Upgrading an OS
===============

After CCE releases a new OS image, if existing nodes cannot be automatically upgraded, you can manually upgrade them in batches.

Precautions
-----------

-  This section describes how to upgrade an OS by resetting the target node. **Workloads running on a node may be interrupted due to standalone deployment or insufficient schedulable resources.** Evaluate the upgrade risks and perform the upgrade during off-peak hours. Alternatively, `specify a disruption budget for your key applications <https://kubernetes.io/docs/tasks/run-application/configure-pdb/>`__ to ensure the availability of these applications during the upgrade.
-  The system disk and data disks of the node will be cleared. **Back up important data** before resetting the node.
-  Resetting a node will clear your configured Kubernetes labels and taints. As a result, resources (such as local storage and load of specified scheduling nodes) associated with the node may be unavailable. Exercise caution when performing this operation to avoid impact on running services.
-  After the OS is upgraded, the node automatically starts.
-  To ensure node stability, CCE will reserve some CPU and memory resources to run necessary system components.

Notes and Constraints
---------------------

-  Nodes running private images cannot be upgraded.
-  Compatibility issues may occur when the node OS of an early version is upgraded. In this case, manually reset the node for the OS upgrade.

Procedure for Default Node Pools
--------------------------------

#. Log in to the CCE console.
#. Click the cluster name to access the cluster console. Choose **Nodes** in the navigation pane. In the right pane, click the **Node Pools** tab.
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

      Pre-installation script command, in which Chinese characters are not allowed. The script command will be Base64-transcoded. The characters of both the pre-installation and post-installation scripts are centrally calculated, and the total number of characters after transcoding cannot exceed 10240.

      The script will be executed before Kubernetes software is installed. Note that if the script is incorrect, Kubernetes software may fail to be installed.

   -  Post-installation script:

      Pre-installation script command, in which Chinese characters are not allowed. The script command will be Base64-transcoded. The characters of both the pre-installation and post-installation scripts are centrally calculated, and the total number of characters after transcoding cannot exceed 10240.

      The script will be executed after Kubernetes software is installed, which does not affect the installation.

#. Click **OK**.

Procedure for Non-default Node Pools
------------------------------------

#. Log in to the CCE console.
#. Click the cluster name to access the cluster console. Choose **Nodes** in the navigation pane. In the right pane, click the **Node Pools** tab.
#. Choose **More > Synchronize** in the **Operation** column of the target node pool.
#. In the **Batch synchronization** window, configure parameters.

   -  **OS**: shows the image of the target version. You do not need to configure this parameter.
   -  **Synchronization Policy**: **Node Reset** is supported.
   -  **Max. Nodes for Batch Synchronize**: maximum number of nodes that will be unavailable during node synchronization. Nodes will be unavailable during synchronization by resetting the nodes. Properly configure this parameter to prevent pod scheduling failures caused by too many unavailable nodes in the cluster.
   -  **Node List**: Select the nodes requiring the synchronization of node pool configurations.

#. Click **OK**.
