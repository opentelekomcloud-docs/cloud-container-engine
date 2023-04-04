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

This section describes how to upgrade a CCE cluster of v1.15 or later. For other versions, see :ref:`Performing Replace/Rolling Upgrade <cce_10_0120>`.

#. Log in to the CCE console and click the cluster name to access the cluster.

#. In the navigation pane, choose **Cluster Upgrade**. You can view the new version available for upgrade on the right.

   Check the version information, last update/upgrade time, available upgrade version, and upgrade history of the current cluster.

   The cluster upgrade goes through pre-upgrade check, add-on upgrade/uninstallation, master node upgrade, worker node upgrade, and post-upgrade processing.

   .. note::

      -  If your cluster version is up-to-date, the **Upgrade** button is grayed out.
      -  If your cluster status is abnormal or there are abnormal add-ons, the **Upgrade** button is dimmed. Perform a check by referring to :ref:`Before You Start <cce_10_0302>`.

#. Click **Upgrade** or **Install Patch** on the right. Set the upgrade parameters.

   -  **New Version**: Select the Kubernetes version to which the cluster can be upgraded.

   -  (Optional) **Cluster Backup**: Determine whether to back up the entire master node. This backup mode is recommended.

      A manual confirmation is required for backing up the entire master node. The backup process uses the Cloud Backup and Recovery (CBR) service and takes about 20 minutes. If there are many cloud backup tasks at the current site, the backup time may be prolonged. You are advised to back up the master node.

   -  **Add-on Upgrade Configuration**: Add-ons that have been installed in your cluster are listed. During the cluster upgrade, the system automatically upgrades the add-ons to be compatible with the target cluster version. You can click **Set** to re-define the add-on parameters.

      .. note::

         If a red dot |image1| is displayed on the right of an add-on, the add-on is incompatible with the target cluster version. During the upgrade, the add-on will be uninstalled and then re-installed. Ensure that the add-on parameters are correctly configured.

   -  **Node Upgrade Configuration**: Before setting the node upgrade priority, you need to select a node pool. Nodes and node pools will be upgraded according to the priorities you specify. You can set the maximum number of nodes to be upgraded in a batch, or set priorities for nodes to be upgraded. If you do not set this parameter, the system will determine the nodes to upgrade in batches based on specific conditions.

      -  **Add Upgrade Priority**: Add upgrade priorities for node pools.
      -  **Add Node Priority**: After adding a node pool priority, you can set the upgrade sequence of nodes in the node pool. The system upgrades nodes in the sequence you specify. If you skip this setting, the system upgrades nodes based on the default policy.

#. Read the upgrade instructions carefully, and select **I have read and agree to Upgrade Precautions**. Click **Upgrade**.

#. After you click **Upgrade**, the cluster upgrade starts. You can view the upgrade process in the lower part of the page.

   During the upgrade, you can click **Suspend** on the right to suspend the cluster upgrade. To continue the upgrade, click **Continue**.

   If an upgrade failure message is displayed during the cluster upgrade, rectify the fault as prompted and try again.

#. When the upgrade progress reaches 100%, the cluster is upgraded. The version information will be properly displayed, and no upgrade is required.

#. After the upgrade is complete, verify the cluster Kubernetes version on the **Clusters** page.

.. |image1| image:: /_static/images/en-us_image_0000001244101223.png
