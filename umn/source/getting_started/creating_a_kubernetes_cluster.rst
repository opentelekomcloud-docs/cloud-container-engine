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

   -  If you have not created a cluster, a wizard page is displayed. Click **Create** under **CCE cluster**.
   -  If you have created a cluster, choose **Clusters** from the navigation pane on the left and click **Create** next to **CCE cluster**.

#. On the **Configure Cluster** page, configure cluster parameters.

   In this example, a majority of parameters retain default values. Only mandatory parameters are described. For details, see :ref:`Table 1 <cce_qs_0008__table462753513248>`.

   .. _cce_qs_0008__table462753513248:

   .. table:: **Table 1** Parameters for creating a cluster

      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                    |
      +===================================+================================================================================================================================================================================================================+
      | **Basic Settings**                |                                                                                                                                                                                                                |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \*Cluster Name                    | Name of the cluster to be created. A cluster name contains 4 to 128 characters starting with a lowercase letter and not ending with a hyphen (-). Only lowercase letters, digits, and hyphens (-) are allowed. |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Enterprise Project             | This parameter is displayed only for enterprise users who have enabled Enterprise Project Management.                                                                                                          |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Cluster Version                | Choose the latest version.                                                                                                                                                                                     |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Cluster Scale                  | Maximum number of **worker nodes** that can be managed by the cluster. If you select **50 nodes**, the cluster can manage a maximum of 50 worker nodes.                                                        |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \*High Availability               | The default value is **Yes**.                                                                                                                                                                                  |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | **Network Settings**              |                                                                                                                                                                                                                |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Network Model                  | Retain the default settings.                                                                                                                                                                                   |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \*VPC                             | VPC where the cluster will be located.                                                                                                                                                                         |
      |                                   |                                                                                                                                                                                                                |
      |                                   | If no VPC is available, click **Create VPC** to create one. After the VPC is created, click refresh.                                                                                                           |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Master Node Subnet             | Subnet where master nodes of the cluster are located.                                                                                                                                                          |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Container CIDR Block           | Retain the default value.                                                                                                                                                                                      |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* IPv4 Service CIDR Block        | CIDR block for Services used by containers in the same cluster to access each other. The value determines the maximum number of Services you can create. The value cannot be changed after creation.           |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Next: Add-on Configuration**. Retain the default settings.

#. Click **Next: Confirm**. After confirming that the cluster configuration information is correct, select **I have read and understand the preceding instructions** and click **Submit**.

   It takes about 6 to 10 minutes to create a cluster.

   The created cluster will be displayed on the **Clusters** page, and the number of nodes in the cluster is 0.

Creating a Node
---------------

After a cluster is created, you need to create nodes in the cluster to run workloads.

#. Log in to the CCE console.

#. Click the created cluster.

#. In the navigation pane, choose **Nodes**. Click **Create Node** in the upper right corner and set node parameters.

   The following describes only important parameters. For other parameters, retain the defaults.

   **Compute Settings**

   -  **AZ**: Retain the default value.
   -  **Node Type**: Select **Elastic Cloud Server (VM)**.
   -  **Specifications**: Select node specifications that fit your business needs.
   -  **Container Engine**: Select a container engine as required.
   -  **OS**: Select the operating system (OS) of the nodes to be created.
   -  **Node Name**: Enter a node name.
   -  **Login Mode**:

      -  If the login mode is **Key pair**, select a key pair for logging to the node and select the check box to acknowledge that you have obtained the key file and without this file you will not be able to log in to the node.

         A key pair is used for identity authentication when you remotely log in to a node. If no key pair is available, click **Create Key Pair**..

   **Storage Settings**

   -  **System Disk**: Set disk type and capacity based on site requirements. The default disk capacity is 50 GB.
   -  **Data Disk**: Set disk type and capacity based on site requirements. The default disk capacity is 100 GB.

   **Network Settings**

   -  **VPC**: Use the default value, that is, the subnet selected during cluster creation.
   -  **Node Subnet**: Select a subnet in which the node runs.
   -  **Node IP**: IP address of the specified node.
   -  **EIP**: The default value is **Do not use**. You can select **Use existing** and **Auto create**.

#. At the bottom of the page, select the node quantity, and click **Next: Confirm**.

#. Review the node specifications, read the instructions, select **I have read and understand the preceding instructions**, and click **Submit**.

   It takes about 6 to 10 minutes to add a node.

   The created node will be displayed on the **Nodes** page.
