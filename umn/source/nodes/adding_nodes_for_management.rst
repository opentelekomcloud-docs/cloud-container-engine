:original_name: cce_10_0198.html

.. _cce_10_0198:

Adding Nodes for Management
===========================

Scenario
--------

In CCE, you can :ref:`Creating a Node <cce_10_0363>` or add existing nodes (ECSs) into your cluster.

.. important::

   -  While an ECS is being accepted into a cluster, the operating system of the ECS will be reset to the standard OS image provided by CCE to ensure node stability. The CCE console prompts you to select the operating system and the login mode during the reset.
   -  The system disk and data disk of an ECS will be formatted while the ECS is being accepted into a cluster. Ensure that information in the disks has been backed up.
   -  While an ECS is being accepted into a cluster, do not perform any operation on the ECS through the ECS console.

Notes and Constraints
---------------------

-  The cluster version must be 1.15 or later.
-  If the password or key has been set when a VM node is created, the VM node can be accepted into a cluster 10 minutes after it is available. During the management, the original password or key will become invalid. You need to reset the password or key.
-  Nodes in a CCE Turbo cluster must support sub-ENIs or be bound to at least 16 ENIs. For details about the node specifications, see the nodes that can be selected on the console when you create a node.

Prerequisites
-------------

A cloud server that meets the following conditions can be accepted:

-  The node to be accepted must be in the **Running** state and not used by other clusters. In addition, the node to be accepted does not carry the CCE-Dynamic-Provisioning-Node tag.
-  The node to be accepted and the cluster must be in the same VPC. (If the cluster version is earlier than v1.13.10, the node to be accepted and the CCE cluster must be in the same subnet.)
-  At least one data disk is attached to the node to be accepted. The data disk capacity is greater than or equal to 100 GB.
-  The node to be accepted has 2-core or higher CPU, 4 GB or larger memory, and only one NIC.
-  Only cloud servers with the same specifications, AZ, and data disk configuration can be added in batches.

Procedure
---------

#. Log in to the CCE console and go to the cluster where the node to be managed resides.

#. In the navigation pane, choose **Nodes**. On the displayed page, click **Accept Node** in the upper right corner.

#. Specify node parameters.

   **Compute Settings**

   .. table:: **Table 1** Configuration parameters

      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                           |
      +===================================+=======================================================================================================================================================================================+
      | Specifications                    | Click **Select Cloud Server** and select the servers to be accepted.                                                                                                                  |
      |                                   |                                                                                                                                                                                       |
      |                                   | You can select multiple cloud servers for batch management. However, only the cloud servers with the same specifications, AZ, and data disk configuration can be added in batches.    |
      |                                   |                                                                                                                                                                                       |
      |                                   | If a cloud server contains multiple data disks, select one of them for the container runtime and kubelet.                                                                             |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Container Engine                  | CCE clusters support Docker. Starting from CCE 1.23, containerd is supported.                                                                                                         |
      |                                   |                                                                                                                                                                                       |
      |                                   | For a CCE Turbo cluster, both **Docker** and **containerd** are supported. For details, see :ref:`Mapping between Node OSs and Container Engines <cce_10_0462__section159298451879>`. |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | OS                                | **Public image**: Select an OS for the node.                                                                                                                                          |
      |                                   |                                                                                                                                                                                       |
      |                                   | **Private image**: You can use private images.                                                                                                                                        |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Login Mode                        | -  **Key Pair**                                                                                                                                                                       |
      |                                   |                                                                                                                                                                                       |
      |                                   |    Select the key pair used to log in to the node. You can select a shared key.                                                                                                       |
      |                                   |                                                                                                                                                                                       |
      |                                   |    A key pair is used for identity authentication when you remotely log in to a node. If no key pair is available, click **Create Key Pair**.                                         |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

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
      | Kubernetes Label                  | Click **Add Label** to set the key-value pair attached to the Kubernetes objects (such as pods). A maximum of 20 labels can be added.                                                                                                                          |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | Labels can be used to distinguish nodes. With workload affinity settings, container pods can be scheduled to a specified node. For more information, see `Labels and Selectors <https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/>`__. |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Resource Tag                      | You can add resource tags to classify resources.                                                                                                                                                                                                               |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | You can create **predefined tags** in Tag Management Service (TMS). Predefined tags are visible to all service resources that support the tagging function. You can use these tags to improve tagging and resource migration efficiency.                       |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | CCE will automatically create the "CCE-Dynamic-Provisioning-Node=\ *node id*" tag.                                                                                                                                                                             |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Taint                             | This field is left blank by default. You can add taints to set anti-affinity for the node. A maximum of 10 taints are allowed for each node. Each taint contains the following parameters:                                                                     |
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

#. Click **Next: Confirm**. Click **Submit**.
