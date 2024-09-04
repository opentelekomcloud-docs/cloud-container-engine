:original_name: cce_10_0003.html

.. _cce_10_0003:

Resetting a Node
================

Scenario
--------

You can reset a node to modify the node configuration, such as the node OS and login mode.

Resetting a node will reinstall the node OS and the Kubernetes software on the node. If a node is unavailable because you modify the node configuration, you can reset the node to rectify the fault.

Notes and Constraints
---------------------

-  For CCE standard clusters and CCE Turbo clusters to support node resetting, the version must be v1.13 or later.

Precautions
-----------

-  Only worker nodes can be reset. If the node is still unavailable after the resetting, delete the node and create a new one.
-  After a node is reset, the node OS will be reinstalled. Before resetting a node, :ref:`drain <cce_10_0605>` the node to gracefully evict the pods running on the node to other available nodes. Perform this operation during off-peak hours.
-  **After a node is reset, its system disk and data disks will be cleared. Back up important data before resetting a node.**
-  **After a worker node with an extra data disk attached is reset on the ECS console, the attachment will be cleared. In this case, attach the disk again and data will be retained.**
-  The IP addresses of the workload pods on the node will change, but the container network access is not affected.
-  There is remaining EVS disk quota.
-  While the node is being deleted, the backend will set the node to the unschedulable state.
-  Resetting a node will clear the Kubernetes labels and taints you added (those added by editing a node pool will not be lost). As a result, node-specific resources (such as local storage and workloads scheduled to this node) may be unavailable.
-  Resetting a node will cause PVC/PV data loss for the :ref:`local PV <cce_10_0391>` associated with the node. These PVCs and PVs cannot be restored or used again. In this scenario, the pod that uses the local PV is evicted from the reset node. A new pod is created and stays in the pending state. This is because the PVC used by the pod has a node label, due to which the pod cannot be scheduled. After the node is reset, the pod may be scheduled to the reset node. In this case, the pod remains in the creating state because the underlying logical volume corresponding to the PVC does not exist.

Resetting Nodes in the Default Pool
-----------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Nodes**. On the displayed page, click the **Nodes** tab.

#. In the node list of the default pool, select one or more nodes to be reset and choose **More** > **Reset Node** in the **Operation** column.

#. In the displayed dialog box, click **Next**.

#. Specify node parameters.

   **Compute Settings**

   .. table:: **Table 1** Configuration parameters

      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                             |
      +===================================+=========================================================================================================================================================================================================================================================================================================================================+
      | Specifications                    | Specifications cannot be modified when you reset a node.                                                                                                                                                                                                                                                                                |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Container Engine                  | The container engines supported by CCE include Docker and containerd, which may vary depending on cluster types, cluster versions, and OSs. Select a container engine based on the information displayed on the CCE console. For details, see :ref:`Mapping between Node OSs and Container Engines <cce_10_0462__section159298451879>`. |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | OS                                | Select an OS type. Different types of nodes support different OSs.                                                                                                                                                                                                                                                                      |
      |                                   |                                                                                                                                                                                                                                                                                                                                         |
      |                                   | -  **Public image**: Select a public image for the node.                                                                                                                                                                                                                                                                                |
      |                                   | -  **Private image**: Select a private image for the node.                                                                                                                                                                                                                                                                              |
      |                                   |                                                                                                                                                                                                                                                                                                                                         |
      |                                   | .. note::                                                                                                                                                                                                                                                                                                                               |
      |                                   |                                                                                                                                                                                                                                                                                                                                         |
      |                                   |    Service container runtimes share the kernel and underlying calls of nodes. To ensure compatibility, select a Linux distribution version that is the same as or close to that of the final service container image for the node OS.                                                                                                   |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Login Mode                        | -  **Key Pair**                                                                                                                                                                                                                                                                                                                         |
      |                                   |                                                                                                                                                                                                                                                                                                                                         |
      |                                   |    Select the key pair used to log in to the node. You can select a shared key.                                                                                                                                                                                                                                                         |
      |                                   |                                                                                                                                                                                                                                                                                                                                         |
      |                                   |    A key pair is used for identity authentication when you remotely log in to a node. If no key pair is available, click **Create Key Pair**.                                                                                                                                                                                           |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   **Storage Settings**

   Configure storage resources on a node for the containers running on it.

   .. table:: **Table 2** Storage configuration parameters

      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                          |
      +===================================+======================================================================================================================================================================================================================================================================================+
      | System Disk                       | Directly use the system disk of the cloud server.                                                                                                                                                                                                                                    |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Data Disk                         | **At least one data disk is required** for the container runtime and kubelet. **The data disk cannot be deleted or uninstalled. Otherwise, the node will be unavailable.**                                                                                                           |
      |                                   |                                                                                                                                                                                                                                                                                      |
      |                                   | Click **Expand** to configure **Data Disk Space Allocation**, which is used to allocate space for container engines, images, and ephemeral storage for them to run properly. For details about how to allocate data disk space, see :ref:`Data Disk Space Allocation <cce_10_0341>`. |
      |                                   |                                                                                                                                                                                                                                                                                      |
      |                                   | For other data disks, a raw disk is created without any processing by default. You can also click **Expand** and select **Mount Disk** to mount the data disk to a specified directory.                                                                                              |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   **Advanced Settings**

   .. table:: **Table 3** Advanced configuration parameters

      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                               |
      +===================================+===========================================================================================================================================================================================================================================================================================================+
      | Resource Tag                      | You can add resource tags to classify resources. A maximum of eight resource tags can be added.                                                                                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                                                                                                           |
      |                                   | You can create **predefined tags** on the TMS console. The predefined tags are available to all resources that support tags. You can use predefined tags to improve the tag creation and resource migration efficiency.                                                                                   |
      |                                   |                                                                                                                                                                                                                                                                                                           |
      |                                   | CCE will automatically create the "CCE-Dynamic-Provisioning-Node=\ *Node ID*" tag.                                                                                                                                                                                                                        |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Kubernetes Label                  | Click **Add Label** to set the key-value pair attached to the Kubernetes objects (such as pods). A maximum of 20 labels can be added.                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                                                                                           |
      |                                   | Labels can be used to distinguish nodes. With workload affinity settings, pods can be scheduled to a specified node. For more information, see `Labels and Selectors <https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/>`__.                                                      |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Taint                             | This field is left blank by default. You can add taints to configure anti-affinity for the node. A maximum of 20 taints are allowed for each node. Each taint contains the following parameters:                                                                                                          |
      |                                   |                                                                                                                                                                                                                                                                                                           |
      |                                   | -  **Key**: A key must contain 1 to 63 characters, starting with a letter or digit. Only letters, digits, hyphens (-), underscores (_), and periods (.) are allowed. A DNS subdomain name can be used as the prefix of a key.                                                                             |
      |                                   | -  **Value**: A value must contain 1 to 63 characters, starting with a letter or digit. Only letters, digits, hyphens (-), underscores (_), and periods (.) are allowed.                                                                                                                                  |
      |                                   | -  **Effect**: Available options are **NoSchedule**, **PreferNoSchedule**, and **NoExecute**.                                                                                                                                                                                                             |
      |                                   |                                                                                                                                                                                                                                                                                                           |
      |                                   | .. important::                                                                                                                                                                                                                                                                                            |
      |                                   |                                                                                                                                                                                                                                                                                                           |
      |                                   |    NOTICE:                                                                                                                                                                                                                                                                                                |
      |                                   |                                                                                                                                                                                                                                                                                                           |
      |                                   |    -  If taints are used, you must configure tolerations in the YAML files of pods. Otherwise, scale-up may fail or pods cannot be scheduled onto the added nodes.                                                                                                                                        |
      |                                   |    -  After a node pool is created, you can click **Edit** to modify its configuration. The modification will be synchronized to all nodes in the node pool.                                                                                                                                              |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Max. Pods                         | Maximum number of pods that can run on the node, including the default system pods.                                                                                                                                                                                                                       |
      |                                   |                                                                                                                                                                                                                                                                                                           |
      |                                   | This limit prevents the node from being overloaded with pods.                                                                                                                                                                                                                                             |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Pre-installation Command          | Pre-installation script command, in which Chinese characters are not allowed. The script command will be Base64-transcoded. The characters of both the pre-installation and post-installation scripts are centrally calculated, and the total number of characters after transcoding cannot exceed 10240. |
      |                                   |                                                                                                                                                                                                                                                                                                           |
      |                                   | The script will be executed before Kubernetes software is installed. Note that if the script is incorrect, Kubernetes software may fail to be installed.                                                                                                                                                  |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Post-installation Command         | Pre-installation script command, in which Chinese characters are not allowed. The script command will be Base64-transcoded. The characters of both the pre-installation and post-installation scripts are centrally calculated, and the total number of characters after transcoding cannot exceed 10240. |
      |                                   |                                                                                                                                                                                                                                                                                                           |
      |                                   | The script will be executed after Kubernetes software is installed, which does not affect the installation.                                                                                                                                                                                               |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Next: Confirm**.

#. Click **Submit**.

Resetting Nodes in a Node Pool
------------------------------

.. note::

   Parameter configurations are not supported when resetting a node you created in a node pool. The image configured for the node pool is used to reset the node.

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Nodes**. On the displayed page, click the **Nodes** tab.
#. In the node list of the target node pool, select a node to be reset and choose **More** > **Reset Node** in the **Operation** column.
#. In the displayed dialog box, click **Yes**.

Resetting Nodes in a Batch
--------------------------

Resetting nodes in a batch varies depending on application scenarios.

+----------------------------------------------------+-----------------------------+--------------------------------------------------------------------------------------------------------------+
| Scenario                                           | Supported or Not            | Description                                                                                                  |
+====================================================+=============================+==============================================================================================================+
| Resetting nodes in the default pool in a batch     | Supported in some scenarios | This operation can be performed only if the flavors, AZs, and disk configurations of all nodes are the same. |
+----------------------------------------------------+-----------------------------+--------------------------------------------------------------------------------------------------------------+
| Resetting nodes in a node pool in a batch          | Supported in some scenarios | This operation can be performed only if the disk configurations of all nodes are the same.                   |
+----------------------------------------------------+-----------------------------+--------------------------------------------------------------------------------------------------------------+
| Resetting nodes in different node pools in a batch | Not supported               | Only the nodes in the same node pool can be reset in a batch.                                                |
+----------------------------------------------------+-----------------------------+--------------------------------------------------------------------------------------------------------------+
