:original_name: cce_10_0655.html

.. _cce_10_0655:

Copying a Node Pool
===================

You can copy the configuration of an existing node pool on the CCE console to create new node pools.

.. note::

   -  There is no node in a newly created node pool. You can manually add nodes to it or enable auto scaling to have nodes added automatically.
   -  When a node pool is copied, only resource settings (such as node flavor, OS, storage, and network) and advanced settings (such as labels and taints) related to node pool creation are copied. Post-creation settings, such as auto scaling and component management, are not copied.

#. Log in to the CCE console.
#. Click the cluster name to access the cluster console. Choose **Nodes** in the navigation pane. In the right pane, click the **Node Pools** tab.
#. Choose **More** > **Copy** in the **Operation** column of the target node pool.
#. On the **Copy Node Pool** page, view the settings of the copied node pool and modify them as needed. **Login Mode** and **Associate Security Group** need to be confirmed separately. Other configuration items inherit the settings of the original node pool. For details, see :ref:`Creating a Node Pool <cce_10_0012>`. After confirming the configuration, click **Next: Confirm**.
#. On the **Confirm** page, confirm the node pool settings, read the usage instructions, select the check box, and click **Submit**. Then, a new node pool is created based on the modified settings.
