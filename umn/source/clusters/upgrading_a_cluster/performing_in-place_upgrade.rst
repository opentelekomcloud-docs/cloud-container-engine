:original_name: cce_10_0301.html

.. _cce_10_0301:

Performing In-place Upgrade
===========================

Scenario
--------

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
-  During the upgrade, CCE checks the add-on running status. Some add-ons (such as coredns) require at least two nodes to run normally. In this case, at least two nodes must be available for the upgrade.

For more information, see :ref:`Before You Start <cce_10_0302>`.

Procedure
---------

The cluster upgrade goes through check, backup, configuration and upgrade, and verification.

#. Log in to the CCE console and access the cluster console.

#. In the navigation pane, choose **Cluster Upgrade**. You can view the recommended version on the right.

#. Select the cluster version to be upgraded and click **Check**.

   .. note::

      -  If your cluster has a new minor version, you do not need to select the cluster version. The latest minor version is used by default.
      -  If your cluster has a new major version, you can select a version as required.
      -  If your cluster is of the latest version, the check entry will be hidden.

#. Click **Start Check** and confirm the check. If there are abnormal or risky items in the cluster, handle the exceptions based on the check results displayed on the page and check again.

   -  **Exceptions**: View the solution displayed on the page, handle the exceptions and check again.
   -  **Risk Items**: may affect the cluster upgrade. Check the risk description and see whether you may be impacted. If no risk exists, click **OK** next to the risk item to manually skip this risk item and check again.

   After the check is passed, click **Next: Back Up**.

#. (Optional) Manually back up the data. Data is backed up during the upgrade following a default policy. You can click **Back Up** to manually back up data. If you do not need to manually back up data, click **Next: Configure & Upgrade**.

#. Configure the upgrade parameters.

   -  **Add-on Upgrade Configuration**: Add-ons that have been installed in your cluster are listed. During the cluster upgrade, the system automatically upgrades the add-ons to be compatible with the target cluster version. You can click **Set** to re-define the add-on parameters.

      .. note::

         If a red dot |image1| is displayed on the right of an add-on, the add-on is incompatible with the target cluster version. During the upgrade, the add-on will be uninstalled and then re-installed. Ensure that the add-on parameters are correctly configured.

   -  **Node Upgrade Configuration:** You can set the maximum number of nodes to be upgraded in a batch.
   -  **Node Priority:** You can set priorities for nodes to be upgraded. If you do not set this parameter, the system will determine the nodes to upgrade in batches based on specific conditions. Before setting the node upgrade priority, you need to select a node pool. Nodes and node pools will be upgraded according to the priorities you specify.

      -  **Add Upgrade Priority**: Add upgrade priorities for node pools.
      -  **Add Node Priority**: After adding a node pool priority, you can set the upgrade sequence of nodes in the node pool. The system upgrades nodes in the sequence you specify. If you skip this setting, the system upgrades nodes based on the default policy.

#. After the configuration is complete, click **Upgrade** and confirm the upgrade. The cluster starts to be upgraded. You can view the process in the lower part of the page.

   During the upgrade, you can click **Suspend** on the right to suspend the cluster upgrade. To continue the upgrade, click **Continue**. When the progress bar reaches 100%, the cluster upgrade is complete.

   .. note::

      If an upgrade failure message is displayed during the cluster upgrade, rectify the fault as prompted and try again.

#. After the upgrade is complete, click **Next: Verify**. Verify the upgrade based on the displayed check items. After confirming that all check items are normal, click **Complete** and confirm that the post-upgrade check is complete.

   You can verify the cluster Kubernetes version on the **Clusters** page.

.. |image1| image:: /_static/images/en-us_image_0000001517743672.png
