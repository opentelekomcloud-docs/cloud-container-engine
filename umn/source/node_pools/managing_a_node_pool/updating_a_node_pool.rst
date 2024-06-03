:original_name: cce_10_0653.html

.. _cce_10_0653:

Updating a Node Pool
====================

Constraints
-----------

-  The modification of resource tags of a node pool takes effect only on new nodes. To synchronize the modification onto existing nodes, manually reset the existing nodes.
-  Changes to Kubernetes labels/taints in a node pool will be automatically synchronized to existing nodes. You do not need to reset these nodes.


Updating a Node Pool
--------------------

#. Log in to the CCE console.

#. Click the cluster name to access the cluster console. Choose **Nodes** in the navigation pane and click the **Node Pools** tab on the right.

#. Click **Update** next to the name of the node pool you will edit. Configure the parameters in the displayed **Update Node Pool** page.

   **Basic Settings**

   .. table:: **Table 1** Basic settings

      ============== =========================================================
      Parameter      Description
      ============== =========================================================
      Node Pool Name Name of the node pool.
      Expected Nodes Change the number of nodes based on service requirements.
      ============== =========================================================

   **Advanced Settings**

   .. table:: **Table 2** Advanced settings

      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                    |
      +===================================+================================================================================================================================================================================================================================================================+
      | Resource Tag                      | You can add resource tags to classify resources.                                                                                                                                                                                                               |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | You can create **predefined tags** on the TMS console. The predefined tags are available to all resources that support tags. You can use predefined tags to improve the tag creation and resource migration efficiency.                                        |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | CCE will automatically create the "CCE-Dynamic-Provisioning-Node=\ *node id*" tag.                                                                                                                                                                             |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | .. note::                                                                                                                                                                                                                                                      |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   |    Modified resource tags automatically take effect on new nodes.                                                                                                                                                                                              |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Kubernetes Label                  | A key-value pair added to a Kubernetes object (such as a pod). After specifying a label, click **Add**. A maximum of 20 labels can be added.                                                                                                                   |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | Labels can be used to distinguish nodes. With workload affinity settings, container pods can be scheduled to a specified node. For more information, see `Labels and Selectors <https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/>`__. |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | .. note::                                                                                                                                                                                                                                                      |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   |    Modified Kubernetes labels automatically take effect on new nodes.                                                                                                                                                                                          |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Taint                             | This field is left blank by default. You can add taints to configure node anti-affinity. A maximum of 20 taints are allowed for each node. Each taint contains the following parameters:                                                                       |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | -  **Key**: A key must contain 1 to 63 characters, starting with a letter or digit. Only letters, digits, hyphens (-), underscores (_), and periods (.) are allowed. A DNS subdomain name can be used as the prefix of a key.                                  |
      |                                   | -  **Value**: A value must start with a letter or digit and can contain a maximum of 63 characters, including letters, digits, hyphens (-), underscores (_), and periods (.).                                                                                  |
      |                                   | -  **Effect**: Available options are **NoSchedule**, **PreferNoSchedule**, and **NoExecute**.                                                                                                                                                                  |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | For details, see :ref:`Managing Node Taints <cce_10_0352>`.                                                                                                                                                                                                    |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | .. note::                                                                                                                                                                                                                                                      |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   |    Modified taints automatically take effect on new nodes                                                                                                                                                                                                      |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Edit key pair                     | Only node pools that use key pairs for login support key pair editing. You can select another key pair.                                                                                                                                                        |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | .. note::                                                                                                                                                                                                                                                      |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   |    The edited key pair automatically takes effect on newly added nodes. For existing nodes, manually reset the nodes for the modification to take effect.                                                                                                      |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Pre-installation Command          | Pre-installation script command, in which Chinese characters are not allowed. The script command will be Base64-transcoded.                                                                                                                                    |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | The script will be executed before Kubernetes software is installed. Note that if the script is incorrect, Kubernetes software may fail to be installed.                                                                                                       |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | .. note::                                                                                                                                                                                                                                                      |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   |    The modified pre-installation command automatically takes effect on newly added nodes. For existing nodes, manually reset the nodes for the modification to take effect.                                                                                    |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Post-installation Command         | Pre-installation script command, in which Chinese characters are not allowed. The script command will be Base64-transcoded.                                                                                                                                    |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | The script will be executed after Kubernetes software is installed, which does not affect the installation.                                                                                                                                                    |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   | .. note::                                                                                                                                                                                                                                                      |
      |                                   |                                                                                                                                                                                                                                                                |
      |                                   |    The modified post-installation command automatically takes effect on newly added nodes. For existing nodes, manually reset the nodes for the modification to take effect.                                                                                   |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. After the configuration, click **OK**.

   After the node pool parameters are updated, go to the **Nodes** page to check whether the node to which the node pool belongs is updated. You can reset the node to synchronize the configuration updates for the node pool.
