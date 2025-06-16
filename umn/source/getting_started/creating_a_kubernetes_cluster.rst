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

   In this example, a majority of parameters retain default values. Only mandatory parameters are described. For details, see :ref:`Table 1 <cce_qs_0008__table462753513248>`.

   .. _cce_qs_0008__table462753513248:

   .. table:: **Table 1** Parameters for creating a cluster

      +--------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                  | Description                                                                                                                                                                                                         |
      +============================================+=====================================================================================================================================================================================================================+
      | **Basic Settings**                         |                                                                                                                                                                                                                     |
      +--------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \*Cluster Name                             | Name of the cluster to be created. A cluster name contains 4 to 128 characters starting with a lowercase letter and not ending with a hyphen (-). Only lowercase letters, digits, and hyphens (-) are allowed.      |
      +--------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \*Cluster Version                          | Choose the latest version.                                                                                                                                                                                          |
      +--------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \*Cluster Scale                            | Maximum number of **worker nodes** that can be managed by the cluster. If you select **Nodes: 50**, the cluster can manage a maximum of 50 worker nodes.                                                            |
      +--------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | **Network Settings**                       |                                                                                                                                                                                                                     |
      +--------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \*Network Model                            | You can select **VPC network** or **Tunnel network**. Retain the default value.                                                                                                                                     |
      +--------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \*VPC                                      | VPC where the cluster will be located.                                                                                                                                                                              |
      |                                            |                                                                                                                                                                                                                     |
      |                                            | If no VPC is available, click **Create VPC** to create one. After the VPC is created, click the refresh icon.                                                                                                       |
      +--------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \*Master Node Subnet                       | Subnet where master nodes of the cluster are located.                                                                                                                                                               |
      +--------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \*Container CIDR Block                     | CIDR block used by containers. The value determines the maximum number of containers in the cluster.                                                                                                                |
      |                                            |                                                                                                                                                                                                                     |
      |                                            | Retain the default value.                                                                                                                                                                                           |
      +--------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Pod IP Addresses Reserved for Each Node | The number of container IP addresses that can be allocated to each node (**alpha.cce/fixPoolMask**) when creating a cluster. This parameter determines the maximum number of pods that can be created on each node. |
      +--------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \*IPv4 Service CIDR Block                  | CIDR block for Services used by containers in the same cluster to access each other. The value determines the maximum number of Services you can create. The value cannot be changed after creation.                |
      |                                            |                                                                                                                                                                                                                     |
      |                                            | Retain the default value.                                                                                                                                                                                           |
      +--------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Next: Select Add-on**. On the page displayed, select the add-ons to be installed during cluster creation.

#. Click **Next: Configure Add-on**. Retain the default settings.

#. Click **Next: Confirm configuration**, confirm the displayed cluster resource list, and click **Submit**.

   It takes about 5 to 10 minutes to create a cluster.

   The created cluster will be displayed on the **Clusters** page, and the number of nodes in the cluster is 0.

Creating a Node
---------------

After a cluster is created, you need to create nodes in the cluster to run workloads.

#. Log in to the CCE console.

#. Click the name of the created cluster to access the cluster console.

#. In the navigation pane, choose **Nodes**. Click the **Nodes** tab, click **Create Node** in the upper right corner, and configure parameters as prompted.

   The following describes only important parameters. For other parameters, retain the defaults.

   .. table:: **Table 2** Parameters for creating a node

      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                   |
      +===================================+===============================================================================================================================================================================================================================+
      | **Configurations**                |                                                                                                                                                                                                                               |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \*AZ                              | Select an AZ for the node as needed. You can select **Random**.                                                                                                                                                               |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \*Node Type                       | Select a node type based on service requirements. Then, the available node flavors will be automatically displayed in the **Specifications** area for you to select.                                                          |
      |                                   |                                                                                                                                                                                                                               |
      |                                   | In this example, **Elastic Cloud Server (VM)** is selected.                                                                                                                                                                   |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \*Specifications                  | Select node specifications that best fit your service needs.                                                                                                                                                                  |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \*Container Engine                | Select a container engine based on service requirements.                                                                                                                                                                      |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \*OS                              | Select an OS for the node.                                                                                                                                                                                                    |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \*Login Mode                      | -  If the login mode is **Key Pair**, select a key pair for logging to the node and select the check box to acknowledge that you have obtained the key file and without this file you will not be able to log in to the node. |
      |                                   |                                                                                                                                                                                                                               |
      |                                   |    A key pair is used for identity authentication when you remotely log in to a node. If no key pair is available, click **Create Key Pair**.                                                                                 |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | **Storage Settings**              |                                                                                                                                                                                                                               |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \*System Disk                     | Select the system disk size based on your service requirements. The default value is 50 GiB.                                                                                                                                  |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \*Data Disk                       | Set the data disk size and quantity based on your service requirements. At least one data disk is required for container runtime and Kubelet components. The default value is 100 GiB.                                        |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | **Network Settings**              |                                                                                                                                                                                                                               |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | VPC/Node Subnet                   | The node subnet selected during cluster creation is used by default. You can choose another subnet instead.                                                                                                                   |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. At the bottom of the page, select the node quantity, and click **Next: Confirm**.

#. Review the node specifications, read the instructions, select **I have read and understand the preceding information**, and click **Submit**.

   It takes about 5 to 10 minutes to complete the node creation.

   The created node will be displayed on the **Nodes** page.
