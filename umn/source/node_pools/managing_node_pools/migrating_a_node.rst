:original_name: cce_10_0656.html

.. _cce_10_0656:

Migrating a Node
================

You can migrate nodes between node pools within a cluster. :ref:`Table 1 <cce_10_0656__table35854544536>` lists migration scenarios.

.. _cce_10_0656__table35854544536:

.. table:: **Table 1** Migration scenarios

   +---------------------------------+---------------------------------+-----------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Migration Scenario              |                                 | Migration                               | Operation                                                                                                                                                                                                               |
   +=================================+=================================+=========================================+=========================================================================================================================================================================================================================+
   | Source Node Pool                | Target Node Pool                |                                         |                                                                                                                                                                                                                         |
   +---------------------------------+---------------------------------+-----------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Custom node pool                | Default node pool (DefaultPool) | Supported                               | :ref:`Migrating Nodes from a Custom Node Pool to the Default Node Pool <cce_10_0656__section124008234509>`                                                                                                              |
   +---------------------------------+---------------------------------+-----------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Default node pool (DefaultPool) | Custom node pool                | Supported by clusters of v1.23 or later | :ref:`Migrating Nodes from the Default Node Pool to a Custom Node Pool <cce_10_0656__section7117453155012>`                                                                                                             |
   +---------------------------------+---------------------------------+-----------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Custom node pool                | Custom node pool                | Not supported for direct migration      | In certain scenarios, you may need to remove a node from management and later re-add it. Note that removing and re-adding nodes are subject to specific restrictions. Migration may not be supported in some scenarios. |
   +---------------------------------+---------------------------------+-----------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_10_0656__section124008234509:

Migrating Nodes from a Custom Node Pool to the Default Node Pool
----------------------------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Nodes** and click the **Node Pools** tab.
#. Click **View Node** in the **Operation** column of the node pool to be migrated.
#. Choose **More** > **Migrate Node** in the **Operation** column of the target node to migrate the node.
#. In the **Migrate Node** dialog box, confirm the information.

   .. note::

      -  The migration does not affect custom resource tags, and Kubernetes labels and taints of the node.
      -  The migration does not affect the kubelet, kube-proxy, container runtime, and node OS settings of the node.
      -  After the migration, system labels **cce.cloud.com** and **cce-nodepool** on the node will be deleted. If an existing workload uses these labels for affinity or anti-affinity scheduling, the existing pods on the node will be stopped and rescheduled when kubelet is restarted.

.. _cce_10_0656__section7117453155012:

Migrating Nodes from the Default Node Pool to a Custom Node Pool
----------------------------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Nodes** and click the **Node Pools** tab.
#. Locate the target node pool and choose **More** > **Migrate Node**.
#. In the **Migrate Node** dialog box, select the nodes that meet the following conditions:

   -  The nodes and the current node pool are deployed in the same VPC and subnet.
   -  The nodes and the current node pool are in the same cloud server group.
   -  The nodes are running and they are from the default node pool.
   -  The flavor, AZ, container runtime, OS, and resource reservation settings of the nodes must match those of the node pool.

#. Click **OK**.

   .. note::

      -  After migration, node pool configurations (including resource tags, Kubernetes labels, taints, and Kubernetes component settings) will overwrite conflicting node settings.
      -  After the migration, the pool's security group will take over the nodes' original security group.
      -  After the migration, the pool's agency will take over the nodes' original agency.
      -  After the migration, the nodes' original login mode will remain unchanged.
      -  Nodes migrated to a node pool after acceptance may be removed during scale-in.
