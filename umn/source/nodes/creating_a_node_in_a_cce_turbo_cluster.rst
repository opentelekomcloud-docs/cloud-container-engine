:original_name: cce_01_0363.html

.. _cce_01_0363:

Creating a Node in a CCE Turbo Cluster
======================================

Prerequisites
-------------

-  At least one CCE Turbo cluster is available. For details on how to create a cluster, see :ref:`Creating a CCE Turbo Cluster <cce_01_0298>`.
-  A key pair has been created for identity authentication upon remote node login.

Notes and Constraints
---------------------

-  During the node creation, software packages are downloaded from OBS using the domain name. You need to use a private DNS server to resolve the OBS domain name, and configure the subnet where the node resides with a private DNS server address. When you create a subnet, the private DNS server is used by default. If you change the subnet DNS, ensure that the DNS server in use can resolve the OBS domain name.
-  Nodes in a CCE Turbo cluster must be the models developed on the QingTian architecture that features software-hardware synergy.
-  CCE Turbo clusters are available only in certain regions.

Procedure for Creating a Node
-----------------------------

After a CCE Turbo cluster is created, you can create nodes for the cluster.

#. Click **Create** **Node** in the card view of the created CCE Turbo cluster. In the **Node Configuration** step, set node parameters by referring to the following tables.

   **Computing configurations:**

   You can configure the specifications and OS of a cloud server, on which your containerized applications run.

   .. table:: **Table 1** Configuration parameters

      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                        |
      +===================================+====================================================================================================================================================================================================================================+
      | AZ                                | AZ where the node is located. Nodes in a cluster can be created in different AZs for higher reliability. The value cannot be changed after creation.                                                                               |
      |                                   |                                                                                                                                                                                                                                    |
      |                                   | You are advised to select **Random** to deploy your node in a random AZ based on the selected node flavor.                                                                                                                         |
      |                                   |                                                                                                                                                                                                                                    |
      |                                   | An AZ is a physical region where resources use independent power supply and networks. AZs are physically isolated but interconnected through an internal network. To enhance workload availability, create nodes in different AZs. |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Container runtime                 | Container runtime used on the node. Different container runtimes support different node specifications and cannot be changed after the node is created.                                                                            |
      |                                   |                                                                                                                                                                                                                                    |
      |                                   | -  **runc**: The runC runtime is used. By default, Docker is selected as the container engine when you create a container on the console.                                                                                          |
      |                                   | -  kata: The Kata runtime is used. If you select this type for both nodes and workloads, the workloads run only on the nodes that use the Kata runtime. containerd is used by default.                                             |
      |                                   |                                                                                                                                                                                                                                    |
      |                                   | For details about common containers and secure containers, see :ref:`Secure Containers and Common Containers <cce_01_0180__section7201124294111>`.                                                                                 |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Specifications                    | Select node specifications that best fit your business needs.                                                                                                                                                                      |
      |                                   |                                                                                                                                                                                                                                    |
      |                                   | Nodes in a CCE Turbo cluster must be the models developed on the QingTian architecture that features software-hardware synergy.                                                                                                    |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | OS                                | **Public image**: Select an OS for the node.                                                                                                                                                                                       |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Node Name                         | Name of the node, which must be unique. When nodes (ECSs) are created in batches, the value of this parameter is used as the name prefix for each ECS.                                                                             |
      |                                   |                                                                                                                                                                                                                                    |
      |                                   | The system generates a default name for you, which can be modified.                                                                                                                                                                |
      |                                   |                                                                                                                                                                                                                                    |
      |                                   | A node name must start with a lowercase letter and cannot end with a hyphen (-). Only digits, lowercase letters, and hyphens (-) are allowed.                                                                                      |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Login Mode                        | -  **Key pair**: Select the key pair used to log in to the node. You can select a shared key.                                                                                                                                      |
      |                                   |                                                                                                                                                                                                                                    |
      |                                   |    A key pair is used for identity authentication when you remotely log in to a node. If no key pair is available, click **Create a key pair**.                                                                                    |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   **Storage configuration**

   Configure storage resources on a node for the containers running on it. Set the disk size according to site requirements.

   .. table:: **Table 2** Configuration parameters

      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                   |
      +===================================+===============================================================================================================================================================================================================================================================+
      | System Disk                       | System disk used by the node OS. The value ranges from 40 GB to 1,024 GB. The default value is 50 GB.                                                                                                                                                         |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Data Disk                         | Data disk used by the container runtime and kubelet on the node. The value ranges from 100 GB to 32,768 GB. The default value is 100 GB. The EVS disk types provided for the data disk are the same as those for the system disk.                             |
      |                                   |                                                                                                                                                                                                                                                               |
      |                                   | .. caution::                                                                                                                                                                                                                                                  |
      |                                   |                                                                                                                                                                                                                                                               |
      |                                   |    CAUTION:                                                                                                                                                                                                                                                   |
      |                                   |    If the data disk is uninstalled or damaged, the Docker service becomes abnormal and the node becomes unavailable. You are advised not to delete the data disk.                                                                                             |
      |                                   |                                                                                                                                                                                                                                                               |
      |                                   | Click **Expand** to set the following parameters:                                                                                                                                                                                                             |
      |                                   |                                                                                                                                                                                                                                                               |
      |                                   | -  **Custom space allocation**: Select this option to define the disk space occupied by the container runtime to store the working directories, container image data, and image metadata.                                                                     |
      |                                   | -  **Encryption**: Data disk encryption safeguards your data. Snapshots generated from encrypted disks and disks created using these snapshots automatically inherit the encryption function.                                                                 |
      |                                   |                                                                                                                                                                                                                                                               |
      |                                   |    -  **Encryption** is not selected by default.                                                                                                                                                                                                              |
      |                                   |    -  After you select **Encryption**, you can select an existing key in the displayed **Encryption Setting** dialog box. If no key is available, click the link next to the drop-down box to create a key. After the key is created, click the refresh icon. |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   **Networking configuration**

   Configure networking resources to allow node and containerized application access.

   .. table:: **Table 3** Configuration parameters

      +-------------+---------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter   | Description                                                                                                                                             |
      +=============+=========================================================================================================================================================+
      | Node Subnet | The node subnet selected during cluster creation is used by default. You can choose another subnet instead. The value cannot be changed after creation. |
      +-------------+---------------------------------------------------------------------------------------------------------------------------------------------------------+

   **Advanced Settings**

   Configure advanced node capabilities such as labels, taints, and startup command.

   .. table:: **Table 4** Advanced configuration parameters

      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                    |
      +===================================+================================================================================================================================================================================================================================================================+
      | Kubernetes Label                  | Click **Add Label** to set the key-value pair attached to the Kubernetes objects (such as pods). A maximum of 10 labels can be added.                                                                                                                          |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | Labels can be used to distinguish nodes. With workload affinity settings, container pods can be scheduled to a specified node. For more information, see `Labels and Selectors <https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/>`__. |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Resource Tags                     | You can add resource tags to classify resources.                                                                                                                                                                                                               |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | You can create **predefined tags** in Tag Management Service (TMS). Predefined tags are visible to all service resources that support the tagging function. You can use these tags to improve tagging and resource migration efficiency.                       |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | CCE will automatically create the "CCE-Dynamic-Provisioning-Node=\ *node id*" tag.                                                                                                                                                                             |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Taints                            | This parameter is left blank by default. You can add taints to set anti-affinity for the node. A maximum of 10 taints are allowed for each node. Each taint contains the following parameters:                                                                 |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | -  **Key**: A key must contain 1 to 63 characters, starting with a letter or digit. Only letters, digits, hyphens (-), underscores (_), and periods (.) are allowed. A DNS subdomain name can be used as the prefix of a key.                                  |
      |                                   | -  **Value**: A value must start with a letter or digit and can contain a maximum of 63 characters, including letters, digits, hyphens (-), underscores (_), and periods (.).                                                                                  |
      |                                   | -  **Effect**: Available options are **NoSchedule**, **PreferNoSchedule**, and **NoExecute**.                                                                                                                                                                  |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | .. important::                                                                                                                                                                                                                                                 |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   |    NOTICE:                                                                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   |    -  If taints are used, you must configure tolerations in the YAML files of pods. Otherwise, scale-out may fail or pods cannot be scheduled onto the added nodes.                                                                                            |
      |                                   |    -  After a node pool is created, you can click **Edit** to modify its configuration. The modification will be synchronized to all nodes in the node pool.                                                                                                   |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Max Pods                          | Maximum number of pods that can run on the node, including the default system pods.                                                                                                                                                                            |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | This limit prevents the node from being overloaded of pods. For details, see :ref:`Maximum Number of Pods That Can Be Created on a Node <cce_01_0348>`.                                                                                                        |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Pre-installation Script           | Enter commands. A maximum of 1,000 characters are allowed.                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | The script will be executed before Kubernetes software is installed. Note that if the script is incorrect, Kubernetes software may fail to be installed. The commands are run to format data disks.                                                            |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Post-installation Script          | Enter commands. A maximum of 1,000 characters are allowed.                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | The script will be executed after Kubernetes software is installed and will not affect the installation. The commands are run to modify Docker parameters.                                                                                                     |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Agency                            | An agency is created by the account administrator on the IAM console. By creating an agency, you can share your cloud server resources with another account, or entrust a more professional person or team to manage your resources.                           |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | If no agency is available, click **Create Agency** on the right to create one.                                                                                                                                                                                 |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Next: Confirm** to review the configurations.

#. Click **Submit**.

   The node list page is displayed. If the node status is **Available**, the node is created successfully. It takes about 6 to 10 minutes to create a node.

#. Click **Back to Node List**. The node is created successfully if it changes to the **Available** state.
