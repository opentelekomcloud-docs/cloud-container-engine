:original_name: cce_10_0003.html

.. _cce_10_0003:

Resetting a Node
================

Scenario
--------

You can reset a node to modify the node configuration, such as the node OS and login mode.

Resetting a node will reinstall the node OS and the Kubernetes software on the node. If a node is unavailable because you modify the node configuration, you can reset the node to rectify the fault.

Constraints
-----------

-  For CCE clusters and CCE Turbo clusters, the version must be v1.13 or later to support node resetting.

Precautions
-----------

-  Only worker nodes can be reset. If the node is still unavailable after the resetting, delete the node and create a new one.
-  **Resetting a node will reinstall the node OS and interrupt workload services running on the node. Therefore, perform this operation during off-peak hours.**
-  **Data in the system disk and Docker data disks will be cleared. Back up important data before resetting the node.**
-  **When an extra data disk is mounted to a node, data in this disk will be cleared if the disk has not been unmounted before the node reset. To prevent data loss, back up data in advance and mount the data disk again after the node reset is complete.**
-  The IP addresses of the workload pods on the node will change, but the container network access is not affected.
-  There is remaining EVS disk quota.
-  While the node is being deleted, the backend will set the node to the unschedulable state.
-  Resetting a node will cause PVC/PV data loss for the :ref:`local PV <cce_10_0391>` associated with the node. These PVCs and PVs cannot be restored or used again. In this scenario, the pod that uses the local PV is evicted from the reset node. A new pod is created and stays in the pending state. This is because the PVC used by the pod has a node label, due to which the pod cannot be scheduled. After the node is reset, the pod may be scheduled to the reset node. In this case, the pod is always in the creating state because the underlying logical volume corresponding to the PVC does not exist.

Procedure
---------

The new console allows you to reset nodes in batches. You can also use a private image to reset nodes in batches.

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. Click the cluster name to access the cluster console. Choose **Nodes** in the navigation pane, and select one or multiple nodes to be reset in the list. Choose **More** > **Reset Node**.

#. In the displayed dialog box, click **Next**.

   -  For nodes in the DefaultPool node pool, the parameter setting page is displayed. Set the parameters by referring to :ref:`4 <cce_10_0003__li1646785611239>`.
   -  For a node you create in a node pool, resetting the node does not support parameter configuration. You can directly use the configuration image of the node pool to reset the node.

#. .. _cce_10_0003__li1646785611239:

   Specify node parameters.

   **Compute Settings**

   .. table:: **Table 1** Configuration parameters

      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                              |
      +===================================+==========================================================================================================================================================================================+
      | Specifications                    | Specifications cannot be modified when you reset a node.                                                                                                                                 |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Container Engine                  | CCE clusters support Docker and containerd in some scenarios.                                                                                                                            |
      |                                   |                                                                                                                                                                                          |
      |                                   | -  VPC network clusters of v1.23 and later versions support containerd. Tunnel network clusters of v1.23.2-r0 and later versions support containerd.                                     |
      |                                   | -  For a CCE Turbo cluster, both **Docker** and **containerd** are supported. For details, see :ref:`Mapping between Node OSs and Container Engines <cce_10_0462__section159298451879>`. |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | OS                                | **Public image**: Select an OS for the node.                                                                                                                                             |
      |                                   |                                                                                                                                                                                          |
      |                                   | **Private image**: You can use private images.                                                                                                                                           |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Login Mode                        | -  **Key Pair**                                                                                                                                                                          |
      |                                   |                                                                                                                                                                                          |
      |                                   |    Select the key pair used to log in to the node. You can select a shared key.                                                                                                          |
      |                                   |                                                                                                                                                                                          |
      |                                   |    A key pair is used for identity authentication when you remotely log in to a node. If no key pair is available, click **Create Key Pair**.                                            |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   **Storage Settings**

   Configure storage resources on a node for the containers running on it.

   .. table:: **Table 2** Configuration parameters

      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                        |
      +===================================+====================================================================================================================================================================================================================================================================================================+
      | System Disk                       | Directly use the system disk of the cloud server.                                                                                                                                                                                                                                                  |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Data Disk                         | **At least one data disk is required** for the container runtime and kubelet. **The data disk cannot be deleted or uninstalled. Otherwise, the node will be unavailable.**                                                                                                                         |
      |                                   |                                                                                                                                                                                                                                                                                                    |
      |                                   | Click **Expand** and select **Allocate Disk Space** to define the disk space occupied by the container runtime to store the working directories, container image data, and image metadata. For details about how to allocate data disk space, see :ref:`Data Disk Space Allocation <cce_10_0341>`. |
      |                                   |                                                                                                                                                                                                                                                                                                    |
      |                                   | For other data disks, a raw disk is created without any processing by default. You can also click **Expand** and select **Mount Disk** to mount the data disk to a specified directory.                                                                                                            |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   **Advanced Settings**

   .. table:: **Table 3** Advanced configuration parameters

      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                    |
      +===================================+================================================================================================================================================================================================================================================================+
      | Kubernetes Label                  | Click **Add** to set the key-value pair attached to the Kubernetes objects (such as pods). A maximum of 20 labels can be added.                                                                                                                                |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | Labels can be used to distinguish nodes. With workload affinity settings, container pods can be scheduled to a specified node. For more information, see `Labels and Selectors <https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/>`__. |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Resource Tag                      | You can add resource tags to classify resources.                                                                                                                                                                                                               |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | You can create **predefined tags** in Tag Management Service (TMS). Predefined tags are available to all service resources that support tags. You can use these tags to improve tagging and resource migration efficiency.                                     |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | CCE will automatically create the "CCE-Dynamic-Provisioning-Node=\ *node id*" tag.                                                                                                                                                                             |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Taint                             | This field is left blank by default. You can add taints to configure anti-affinity for the node. A maximum of 20 taints are allowed for each node. Each taint contains the following parameters:                                                               |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | -  **Key**: A key must contain 1 to 63 characters, starting with a letter or digit. Only letters, digits, hyphens (-), underscores (_), and periods (.) are allowed. A DNS subdomain name can be used as the prefix of a key.                                  |
      |                                   | -  **Value**: A value must start with a letter or digit and can contain a maximum of 63 characters, including letters, digits, hyphens (-), underscores (_), and periods (.).                                                                                  |
      |                                   | -  **Effect**: Available options are **NoSchedule**, **PreferNoSchedule**, and **NoExecute**.                                                                                                                                                                  |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | .. important::                                                                                                                                                                                                                                                 |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   |    NOTICE:                                                                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   |    -  If taints are used, you must configure tolerations in the YAML files of pods. Otherwise, scale-up may fail or pods cannot be scheduled onto the added nodes.                                                                                             |
      |                                   |    -  After a node pool is created, you can click **Edit** to modify its configuration. The modification will be synchronized to all nodes in the node pool.                                                                                                   |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Max. Pods                         | Maximum number of pods that can run on the node, including the default system pods.                                                                                                                                                                            |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | This limit prevents the node from being overloaded with pods.                                                                                                                                                                                                  |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Pre-installation Command          | Enter commands. A maximum of 1,000 characters are allowed.                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | The script will be executed before Kubernetes software is installed. Note that if the script is incorrect, Kubernetes software may fail to be installed.                                                                                                       |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Post-installation Command         | Enter commands. A maximum of 1,000 characters are allowed.                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | The script will be executed after Kubernetes software is installed and will not affect the installation.                                                                                                                                                       |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Next: Confirm**.

#. Click **Submit**.
