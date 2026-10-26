:original_name: cce_qs_0008.html

.. _cce_qs_0008:

Creating a Kubernetes Cluster
=============================

Context
-------

This section describes how to quickly create a CCE cluster. In this example, the default or simple configurations are in use.

Creating a Cluster
------------------

#. Log in to the CCE console.

   -  If you have no clusters, click **Create Cluster** on the wizard page.
   -  If you have CCE clusters, choose **Clusters** in the navigation pane, click **Create Cluster** and select the CCE standard cluster.

#. On the **Create Cluster** page, configure parameters following instructions.

   Only mandatory parameters are described in this example. You can keep the default values for most other parameters. For details, see :ref:`Table 1 <cce_qs_0008__table462753513248>`.

   .. _cce_qs_0008__table462753513248:

   .. table:: **Table 1** Parameters for creating a cluster

      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                    |
      +===================================+================================================================================================================================================================================================================+
      | **Basic Settings**                |                                                                                                                                                                                                                |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Cluster Name                   | Name of the cluster to be created. A cluster name contains 4 to 128 characters starting with a lowercase letter and not ending with a hyphen (-). Only lowercase letters, digits, and hyphens (-) are allowed. |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Cluster Version                | Choose the latest version.                                                                                                                                                                                     |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Cluster Scale                  | Maximum number of **worker nodes** that can be managed by the cluster. If you select **Nodes: 50**, the cluster can manage a maximum of 50 worker nodes.                                                       |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | **Network Settings**              |                                                                                                                                                                                                                |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Network Model                  | You can select **VPC network** or **Tunnel network**. Retain the default value.                                                                                                                                |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* VPC                            | VPC where the cluster will be located.                                                                                                                                                                         |
      |                                   |                                                                                                                                                                                                                |
      |                                   | If no VPC is available, click **Create VPC** to create one. After creating the VPC, click the refresh icon.                                                                                                    |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Master Node Subnet             | Subnet where master nodes of the cluster are located.                                                                                                                                                          |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Container CIDR Block           | CIDR block used by containers. It controls how many pods can run in the cluster.                                                                                                                               |
      |                                   |                                                                                                                                                                                                                |
      |                                   | Retain the default value.                                                                                                                                                                                      |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* IPv4 Service CIDR Block        | CIDR block for Services used by containers in the same cluster to access each other. The value determines the maximum number of Services you can create. The value cannot be changed after creation.           |
      |                                   |                                                                                                                                                                                                                |
      |                                   | Retain the default value.                                                                                                                                                                                      |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Next: Select Add-on**. On the page displayed, select the add-ons to be installed during cluster creation.

#. Click **Next: Configure Add-on**. Retain the default settings.

#. Click **Next: Confirm Settings**, check the displayed cluster resource list, and click **Submit**.

   Wait for the cluster to be created. It takes approximately 5 to 10 minutes.

   The newly created cluster in the **Running** state with zero nodes will be displayed on the **Clusters** page.

Creating a Node
---------------

After a cluster is created, you need to create nodes in the cluster to run workloads.

#. Log in to the CCE console.

#. Click the name of the cluster to access the cluster console.

#. In the navigation pane, choose **Nodes**. In the right pane, click the **Nodes** tab, click **Create Node** in the upper right corner, and configure parameters following instructions.

   Only mandatory parameters are described in this example. You can keep the default values for most other parameters.

   .. table:: **Table 2** Parameters for creating a node

      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                            |
      +===================================+========================================================================================================================================================================================+
      | **Configurations**                |                                                                                                                                                                                        |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* AZ                             | Select an AZ for the node as needed. You can select **Random**.                                                                                                                        |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Node Type                      | Select a node type based on service requirements. The available node flavors will then be displayed in the **Specifications** area for you to choose from.                             |
      |                                   |                                                                                                                                                                                        |
      |                                   | In this example, **Elastic Cloud Server (VM)** is selected.                                                                                                                            |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Specifications                 | Select a node flavor that best fits your service needs.                                                                                                                                |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Container Engine               | Select a container engine based on service requirements.                                                                                                                               |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* OS                             | Select an OS for the node.                                                                                                                                                             |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Login Mode                     | -  **Key Pair**: Select a key pair for node login and confirm that you have the key file. Without this file, you will not be able to log in.                                           |
      |                                   |                                                                                                                                                                                        |
      |                                   |    Key pairs are used for identity authentication when you remotely access nodes. If you do not have a key pair, click **Create Key Pair**.                                            |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | **Storage Settings**              |                                                                                                                                                                                        |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* System Disk                    | Select the system disk size based on your service requirements. The default value is 50 GiB.                                                                                           |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Data Disk                      | Set the data disk size and quantity based on your service requirements. At least one data disk is required for container runtime and kubelet components. The default value is 100 GiB. |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | **Network Settings**              |                                                                                                                                                                                        |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* VPC/Node Subnet                | The node subnet selected during cluster creation is used by default. You can choose another subnet instead.                                                                            |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. At the bottom of the page, select the node quantity, and click **Next: Confirm**.

#. Review the node specifications, read the instructions, select **I have read and understand the preceding information**, and click **Submit**.

   Wait for the nodes to be created. It takes approximately 5 to 10 minutes.

   The created node will be displayed on the **Nodes** page.
