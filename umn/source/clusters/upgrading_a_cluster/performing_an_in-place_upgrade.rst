:original_name: cce_10_0301.html

.. _cce_10_0301:

Performing an In-place Upgrade
==============================

You can upgrade your clusters to a newer version on the CCE console.

Before the upgrade, learn about the target version to which each CCE cluster can be upgraded in what ways, and the upgrade impacts. For details, see :ref:`Upgrade Overview <cce_10_0197>` and :ref:`Before You Start <cce_10_0302>`.

Description
-----------

-  An in-place upgrade updates the Kubernetes components on cluster nodes, without changing their OS version.
-  Data plane nodes are upgraded in batches. By default, they are prioritized based on their CPU, memory, and `PodDisruptionBudgets (PDBs) <https://kubernetes.io/docs/tasks/run-application/configure-pdb/>`__. You can also set the priorities according to your service requirements.

Precautions
-----------

-  During the cluster upgrade, the system will automatically upgrade add-ons to a version compatible with the target cluster version. Do not uninstall or reinstall add-ons during the cluster upgrade.
-  Before the upgrade, ensure that all add-ons are running. If an add-on fails to be upgraded, rectify the fault and try again.
-  During the upgrade, CCE checks the add-on running status. Some add-ons (such as CoreDNS) require at least two nodes to run properly. In this case, at least two nodes must be available for the upgrade.

For more information, see :ref:`Before You Start <cce_10_0302>`.

Procedure
---------

The cluster upgrade goes through check, backup, configuration and upgrade, and verification.

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Cluster Upgrade**.

#. CCE automatically provides you with an optimal upgrade path based on the current cluster version. Select the target cluster version, check information such as version differences, node OS version, and add-on versions, and click **Check**.

#. Perform the pre-upgrade check. Click **Start Check** and confirm the check. If there are abnormal or risky items in the cluster, handle the exceptions based on the check results displayed on the page and check again.

   -  **Exceptions**: View the solution displayed on the page, handle the exceptions and check again.
   -  **Risk Items**: may affect the cluster upgrade. Check the risk description and see whether you may be impacted. If no risk exists, click **OK** next to the risk item to manually skip this risk item and check again.

   After the check is passed, click **Next**.

#. Back up the cluster. During the cluster upgrade, CCE automatically backs up etcd data. You can manually back up master nodes to speed up the rollback if the master nodes fail to upgrade. If manual backup is not required, click **Next**.

   +-------------------------+------------------------------------------------------------------------------------+----------------------------------------------------+-------------------------------------------------------------------------------+---------------+----------------------------------------------------------------------+
   | Backup Type             | Backup Object                                                                      | Backup Mode                                        | Backup Time                                                                   | Rollback Time | Description                                                          |
   +=========================+====================================================================================+====================================================+===============================================================================+===============+======================================================================+
   | etcd data backup        | etcd data                                                                          | Automatic backup during the upgrade                | 1-5 minutes                                                                   | 2 hours       | Mandatory. The backup is automatically performed during the upgrade. |
   +-------------------------+------------------------------------------------------------------------------------+----------------------------------------------------+-------------------------------------------------------------------------------+---------------+----------------------------------------------------------------------+
   | CBR cloud server backup | Master node disks, including component images, configurations, logs, and etcd data | One-click backup on web pages (manually triggered) | 20 minutes to 2 hours (based on the cloud backup tasks in the current region) | 20 minutes    | This function is gradually replaced by EVS snapshot backup.          |
   +-------------------------+------------------------------------------------------------------------------------+----------------------------------------------------+-------------------------------------------------------------------------------+---------------+----------------------------------------------------------------------+

#. Configure the upgrade parameters.

   -  **Add-on Upgrade Configuration**: Add-ons that have been installed in your cluster are listed. During the cluster upgrade, CCE automatically upgrades the selected add-ons to be compatible with the target cluster version. You can click **Set** to re-define the add-on parameters.

      .. note::

         If an add-on is marked with |image1| on its right side, the add-on cannot be compatible with both the source and target versions of the cluster upgrade. In this case, CCE will upgrade the add-on after the cluster upgrade. The add-on may be unavailable during the cluster upgrade.

   -  **Node Upgrade Configuration:** You can set the maximum number of nodes to be upgraded in a batch.

      Node pools will be upgraded in sequence. Nodes in the same node pool will be upgraded in batches. One node is upgraded in the first batch, two nodes in the second batch, and the number of nodes to be upgraded in each subsequent batch increases by a power of 2 until the maximum number of nodes to be upgraded in each batch is reached.

   -  **Node Priority:** You can set priorities for nodes to be upgraded. If you do not set this parameter, the system will determine the nodes to upgrade in batches based on specific conditions. Before setting the node upgrade priority, select a node pool. Nodes and node pools will be upgraded according to the priorities you specify.

      -  **Add Upgrade Priority**: Add upgrade priorities for node pools.
      -  **Add Node Priority**: After adding a node pool priority, you can set the upgrade sequence of nodes in the node pool. The system upgrades nodes in the sequence you specify. If you skip this setting, the system upgrades nodes based on the default policy.

#. After the configuration is complete, click **Upgrade** and confirm the upgrade. The cluster starts to be upgraded. You can view the process in the lower part of the page.

   .. note::

      If an upgrade failure message is displayed during the cluster upgrade, rectify the fault as prompted and try again.

#. After the upgrade is complete, click **Next**. Verify the upgrade based on the displayed check items. After confirming that all check items are normal, click **Complete** and confirm that the post-upgrade check is complete. For details, see :ref:`Performing Post-Upgrade Verification <cce_10_0560>`.

   You can verify the cluster Kubernetes version on the **Clusters** page.

.. |image1| image:: /_static/images/en-us_image_0000001750950640.png
