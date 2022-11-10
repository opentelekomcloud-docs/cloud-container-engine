:original_name: cce_01_0301.html

.. _cce_01_0301:

Performing In-place Upgrade (v1.15 and Later)
=============================================

Scenario
--------

On the CCE console, You can perform an in-place cluster upgrade to use new cluster features.

Before the upgrade, learn about the target version to which each CCE cluster can be upgraded in what ways, and the upgrade impacts. For details, see :ref:`Overview <cce_01_0197>` and :ref:`Before You Start <cce_01_0302>`.

Description
-----------

-  An in-place upgrade updates the Kubernetes components on cluster nodes, without changing their OS version.
-  Data plane nodes are upgraded in batches. By default, they are prioritized based on their CPU, memory, and `PodDisruptionBudgets (PDBs) <https://kubernetes.io/docs/tasks/run-application/configure-pdb/>`__. You can also set the priorities according to your service requirements.

Precautions
-----------

-  During the cluster upgrade, the system will automatically upgrade add-ons to a version compatible with the target cluster version. Do not uninstall or reinstall add-ons during the cluster upgrade.
-  Before the upgrade, ensure that all add-ons are running. If an add-on fails to be upgraded, rectify the fault and try again.
-  During the upgrade, CCE checks the add-on running status. Some add-ons (such as coredns) require at least two nodes to run normally. In this case, at least two nodes must be available for the upgrade.

For more information, see :ref:`Before You Start <cce_01_0302>`.

Procedure
---------

This section describes how to upgrade a CCE cluster of v1.15 or later. For other versions, see :ref:`Performing Replace/Rolling Upgrade (v1.13 and Earlier) <cce_01_0120>`.

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Clusters**. In the cluster list, view the cluster version.

#. Click **More** for the cluster you want to upgrade, and select **Upgrade** from the drop-down menu.


   .. figure:: /_static/images/en-us_image_0000001229793402.png
      :alt: **Figure 1** Upgrading a cluster

      **Figure 1** Upgrading a cluster

   .. note::

      -  If your cluster version is up-to-date, the **Upgrade** button is grayed out.
      -  If the cluster status is **Unavailable**, the upgrade flag in the upper right corner of the cluster card view will be grayed out. Check the cluster status by referring to :ref:`Before You Start <cce_01_0302>`.

#. (Optional) On the cluster upgrade confirmation page, click **Backup** to back up the entire master node. This backup mode is recommended.

   A manual confirmation is required for backing up the entire master node. The backup process uses the Cloud Backup and Recovery (CBR) service and takes about 20 minutes. If there are many cloud backup tasks at the current site, the backup time may be prolonged. You are advised to back up the master node.


   .. figure:: /_static/images/en-us_image_0000001280171657.png
      :alt: **Figure 2** Determining whether to back up the entire master node

      **Figure 2** Determining whether to back up the entire master node

#. Check the version information, last update/upgrade time, available upgrade version, and upgrade history of the current cluster.

   The cluster upgrade goes through pre-upgrade check, add-on upgrade/uninstallation, master node upgrade, worker node upgrade, and post-upgrade processing.


   .. figure:: /_static/images/en-us_image_0000001274316069.png
      :alt: **Figure 3** Cluster upgrade page

      **Figure 3** Cluster upgrade page

#. Click **Upgrade** on the right. Set the upgrade parameters.

   -  **Available Versions**: Select v1.19 in this example.
   -  **Cluster Backup**: A manual confirmation is required for backing up the entire master node. The backup process uses the Cloud Backup and Recovery (CBR) service and takes about 20 minutes. If there are many cloud backup tasks at the current site, the backup time may be prolonged.
   -  **Add-on Upgrade Configuration**: Add-ons that have been installed in your cluster are listed. During the cluster upgrade, the system automatically upgrades the add-ons to be compatible with the target cluster version. You can click **Set** to re-define the add-on parameters.

      .. note::

         If a red dot |image1| is displayed on the right of an add-on, the add-on is incompatible with the target cluster version. During the upgrade, the add-on will be uninstalled and then re-installed. Ensure that the add-on parameters are correctly configured.

   -  **Node Upgrade Configuration**: Before setting the node upgrade priority, you need to select a node pool. Nodes and node pools will be upgraded according to the priorities you specify. You can set the maximum number of nodes to be upgraded in batch, or set priorities for nodes to be upgraded. If you do not set this parameter, the system will determine the nodes to upgrade in batches based on specific conditions.

      -  **Add Upgrade Priority**: Add upgrade priorities for node pools.
      -  **Add Node Priority**: After adding a node pool priority, you can set the upgrade sequence of nodes in the node pool. The system upgrades nodes in the sequence you specify. If you skip this setting, the system upgrades nodes based on the default policy.


   .. figure:: /_static/images/en-us_image_0000001229794946.png
      :alt: **Figure 4** Configuring upgrade parameters

      **Figure 4** Configuring upgrade parameters

#. Read the upgrade instructions carefully, and select **I have read the upgrade instructions**. Click **Upgrade**.


   .. figure:: /_static/images/en-us_image_0000001280421317.png
      :alt: **Figure 5** Final step before upgrade

      **Figure 5** Final step before upgrade

#. After you click **Upgrade**, the cluster upgrade starts. You can view the upgrade process in the lower part of the page.

   During the upgrade, you can click **Suspend** on the right to suspend the cluster upgrade. To continue the upgrade, click **Continue**.


   .. figure:: /_static/images/en-us_image_0000001280181541.png
      :alt: **Figure 6** Cluster upgrade in process

      **Figure 6** Cluster upgrade in process

#. When the upgrade progress reaches 100%, the cluster is upgraded. The version information will be properly displayed, and no upgrade is required.


   .. figure:: /_static/images/en-us_image_0000001236582394.png
      :alt: **Figure 7** Upgrade completed

      **Figure 7** Upgrade completed

#. After the upgrade is complete, verify the cluster Kubernetes version on the **Clusters** page.


   .. figure:: /_static/images/en-us_image_0000001236263298.png
      :alt: **Figure 8** Verifying the upgrade success

      **Figure 8** Verifying the upgrade success

.. |image1| image:: /_static/images/en-us_image_0000001159118361.png
