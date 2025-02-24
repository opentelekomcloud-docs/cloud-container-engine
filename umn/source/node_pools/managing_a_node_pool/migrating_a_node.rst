:original_name: cce_10_0656.html

.. _cce_10_0656:

Migrating a Node
================

You can migrate nodes between node pools within a cluster. :ref:`Table 1 <cce_10_0656__table35854544536>` lists migration scenarios.

.. _cce_10_0656__table35854544536:

.. table:: **Table 1** Migration scenarios

   +---------------------------------+---------------------------------+-----------------------------------------+-------------------------------------------------------------------------------------------------------------+
   | Migration Scenario              |                                 | Migration                               | Operation                                                                                                   |
   +=================================+=================================+=========================================+=============================================================================================================+
   | Source Node Pool                | Target Node Pool                |                                         |                                                                                                             |
   +---------------------------------+---------------------------------+-----------------------------------------+-------------------------------------------------------------------------------------------------------------+
   | Custom node pool                | Default node pool (DefaultPool) | Supported                               | :ref:`Migrating Nodes from a Custom Node Pool to the Default Node Pool <cce_10_0656__section124008234509>`  |
   +---------------------------------+---------------------------------+-----------------------------------------+-------------------------------------------------------------------------------------------------------------+
   | Default node pool (DefaultPool) | Custom node pool                | Supported by clusters of v1.23 or later | :ref:`Migrating Nodes from the Default Node Pool to a Custom Node Pool <cce_10_0656__section7117453155012>` |
   +---------------------------------+---------------------------------+-----------------------------------------+-------------------------------------------------------------------------------------------------------------+
   | Custom node pool                | Custom node pool                | Not supported                           | None                                                                                                        |
   +---------------------------------+---------------------------------+-----------------------------------------+-------------------------------------------------------------------------------------------------------------+

.. _cce_10_0656__section124008234509:

Migrating Nodes from a Custom Node Pool to the Default Node Pool
----------------------------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Nodes** and click the **Node Pools** tab.
#. Click **View Node** in the **Operation** column of the node pool to be migrated.
#. Choose **More** > **Migrate Node** in the **Operation** column of the target node to migrate the node.
#. In the displayed **Migrate Node** dialog box, confirm the information.

   .. note::

      -  The migration does not affect custom resource tags, Kubernetes labels, and taints of the node.
      -  After the migration, system labels **cce.cloud.com** and **cce-nodepool** on the node will be deleted. If an existing workload uses these labels for affinity or anti-affinity scheduling, the existing pods on the node will be stopped and rescheduled when kubelet is restarted.

.. _cce_10_0656__section7117453155012:

Migrating Nodes from the Default Node Pool to a Custom Node Pool
----------------------------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Nodes** and click the **Node Pools** tab.
#. Locate the target node pool and choose **More** > **Accept Node**.
#. In the **Accept Node** dialog box, select the nodes that meet the following conditions:

   -  The nodes and the current node pool are deployed in the same VPC and subnet.
   -  The nodes and the current node pool belong to the same enterprise project.
   -  The nodes and the current node pool are in the same cloud server group.
   -  The billing mode of the nodes is supported by the current node pool.
   -  The nodes are running and they are from the default node pool.
   -  The flavor, AZ, resource reservation, container engine, and OS configurations of the nodes are the same as those of the node pool.

#. Click **OK**.

   .. note::

      -  After nodes are migrated into a node pool, the pool's resource tags, Kubernetes labels, and Kubernetes taints will be synchronized to the nodes. If there is a conflict, the node pool's configurations will take precedence.
      -  After the migration, the pool's security group will take over the nodes' original security group.
      -  After the migration, the pool's agency will take over the nodes' original agency.
      -  After the migration, the nodes' original login mode will remain unchanged.
      -  After a node is migrated to a node pool after being accepted by the cluster, its acceptance tag will be removed. Scaling in the node pool may result in the removal of the node.
