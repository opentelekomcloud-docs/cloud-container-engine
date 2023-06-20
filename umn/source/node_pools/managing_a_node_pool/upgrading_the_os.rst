:original_name: cce_10_0660.html

.. _cce_10_0660:

Upgrading the OS
================

When CCE releases a new OS image, existing nodes cannot be automatically upgraded. You can manually upgrade them in batches.

.. important::

   -  This operation will upgrade the OS by resetting the node. **Workloads running on the node may be interrupted due to single-instance deployment or insufficient schedulable resources.** Evaluate the upgrade risks and upgrade the OS during off-peak hours, you can also set the Pod Disruption Budget (PDB, that is `disruption budget <https://kubernetes.io/docs/tasks/run-application/configure-pdb/>`__) policy for important applications to ensure their availability.
   -  Nodes that use private images cannot be upgraded.

Procedure
---------

**Default node pool**

#. Log in to the CCE console.
#. Click the cluster name and access the cluster console. Choose **Nodes** in the navigation pane and click the **Node Pools** tab on the right.
#. Click **Upgrade** next to the default node pool
#. In the displayed **Operating system upgrade** window, set upgrade parameters.

   -  **Max. Unavailable Nodes**: specifies the maximum number of unavailable nodes during node synchronization.

   -  **Target Operating System**: You do not need to set this parameter. It is used to display the image information of the target version.

   -  **Node List**: Select the nodes to be upgraded.

   -  Login Mode:

      -  **Key Pair**

         Select the key pair used to log in to the node. You can select a shared key.

         A key pair is used for identity authentication when you remotely log in to a node. If no key pair is available, click **Create Key Pair**.

   -  **Pre-installation Command**: Enter a maximum of 1,000 characters.

      The script will be executed before Kubernetes software is installed. Note that if the script is incorrect, Kubernetes software may fail to be installed.

   -  **Post-installation Command**: Enter a maximum of 1,000 characters.

      The script will be executed after Kubernetes software is installed and will not affect the installation.

#. Click **OK**.

**Non-default node pool**

#. Log in to the CCE console.
#. Click the cluster name and access the cluster console. Choose **Nodes** in the navigation pane and click the **Node Pools** tab on the right.
#. Choose **More > Synchronize** next to a node pool name.
#. In the displayed **Batch synchronization** dialog box, set upgrade parameters.

   -  **Max. Unavailable Nodes**: specifies the maximum number of unavailable nodes during node synchronization.
   -  **Node List**: This parameter cannot be set. By default, nodes that can be upgraded are selected.

#. Click **OK**.
