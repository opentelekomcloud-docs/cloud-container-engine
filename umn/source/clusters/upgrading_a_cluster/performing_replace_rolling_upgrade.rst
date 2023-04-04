:original_name: cce_10_0120.html

.. _cce_10_0120:

Performing Replace/Rolling Upgrade
==================================

Scenario
--------

You can upgrade your clusters to a newer Kubernetes version on the CCE console.

Before the upgrade, learn about the target version to which each CCE cluster can be upgraded in what ways, and the upgrade impacts. For details, see :ref:`Upgrade Overview <cce_10_0197>` and :ref:`Before You Start <cce_10_0302>`.

Precautions
-----------

-  If the coredns add-on needs to be upgraded during the cluster upgrade, ensure that the number of nodes is greater than or equal to the number of coredns instances and all coredns instances are running. Otherwise, the upgrade will fail. Before upgrading a cluster of v1.13, you need to upgrade the coredns add-on to the latest version available for the cluster.
-  When a cluster of v1.11 or earlier is upgraded to v1.13, the impacts on the cluster are as follows:

   -  All cluster nodes will be restarted as their OSs are upgraded, which affects application running.
   -  The cluster signature certificate mechanism is changed. As a result, the original cluster certificate becomes invalid. You need to obtain the certificate or kubeconfig file again after the cluster is upgraded.

-  During the upgrade from one release of v1.13 to a later release of v1.13, applications in the cluster are interrupted for a short period of time only during the upgrade of network components.
-  During the upgrade from Kubernetes 1.9 to 1.11, the kube-dns of the cluster will be uninstalled and replaced with CoreDNS, which may cause loss of the cascading DNS configuration in the kube-dns or temporary interruption of the DNS service. Back up the DNS address configured in the kube-dns so you can configure the domain name in the CoreDNS again when domain name resolution is abnormal.

Procedure
---------

#. Log in to the CCE console and click the cluster name to access the cluster.

#. In the navigation pane, choose **Cluster Upgrade**. You can view the new version available for upgrade on the right. Click **Upgrade**.

   .. note::

      -  If your cluster version is up-to-date, the **Upgrade** button is grayed out.
      -  If your cluster status is abnormal or there are abnormal add-ons, the **Upgrade** button is dimmed. Perform a check by referring to :ref:`Before You Start <cce_10_0302>`.

#. In the displayed **Pre-upgrade Check** dialog box, click **Check Now**.

#. The pre-upgrade check starts. While the pre-upgrade check is in progress, the cluster status will change to **Pre-checking** and new nodes/applications will not be able to be deployed on the cluster. However, existing nodes and applications will not be affected. It takes 3 to 5 minutes to complete the pre-upgrade check.

#. When the status of the pre-upgrade check is **Completed**, click **Upgrade**.

#. On the cluster upgrade page, review or configure basic information by referring to :ref:`Table 1 <cce_10_0120__table924319911495>`.

   .. _cce_10_0120__table924319911495:

   .. table:: **Table 1** Basic information

      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                       |
      +===================================+===================================================================================================================================================================================================+
      | Cluster Name                      | Review the name of the cluster to be upgraded.                                                                                                                                                    |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Current Version                   | Review the version of the cluster to be upgraded.                                                                                                                                                 |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Target Version                    | Review the target version after the upgrade.                                                                                                                                                      |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Node Upgrade Policy               | **Replace** (replace upgrade): Worker nodes will be reset. Their OSs will be reinstalled, and data on the system and data disks will be cleared. Exercise caution when performing this operation. |
      |                                   |                                                                                                                                                                                                   |
      |                                   | .. note::                                                                                                                                                                                         |
      |                                   |                                                                                                                                                                                                   |
      |                                   |    -  The lifecycle management function of the nodes and workloads in the cluster is unavailable.                                                                                                 |
      |                                   |    -  APIs cannot be called temporarily.                                                                                                                                                          |
      |                                   |    -  Running workloads will be interrupted because nodes are reset during the upgrade.                                                                                                           |
      |                                   |    -  Data in the system and data disks on the worker nodes will be cleared. Back up important data before resetting the nodes.                                                                   |
      |                                   |    -  Data disks without LVM mounted to worker nodes need to be mounted again after the upgrade, and data on the disks will not be lost during the upgrade.                                       |
      |                                   |    -  The EVS disk quota must be greater than 0.                                                                                                                                                  |
      |                                   |    -  The container IP addresses change, but the communication between containers is not affected.                                                                                                |
      |                                   |    -  Custom labels on the worker nodes will be cleared.                                                                                                                                          |
      |                                   |    -  It takes about 12 minutes to complete the cluster upgrade.                                                                                                                                  |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Login Mode                        | **Key Pair**                                                                                                                                                                                      |
      |                                   |                                                                                                                                                                                                   |
      |                                   | Select the key pair used to log in to the node. You can select a shared key.                                                                                                                      |
      |                                   |                                                                                                                                                                                                   |
      |                                   | A key pair is used for identity authentication when you remotely log in to a node. If no key pair is available, click **Create Key Pair**.                                                        |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Next**. In the dialog box displayed, click **OK**.

#. Upgrade add-ons. If an add-on needs to be upgraded, a red dot is displayed. Click the **Upgrade** button in the lower left corner of the add-on card view. After the upgrade is complete, click **Upgrade** in the lower right corner of the page.

   .. note::

      -  Master nodes will be upgraded first, and then the worker nodes will be upgraded concurrently. If there are a large number of worker nodes, they will be upgraded in different batches.
      -  Select a proper time window for the upgrade to reduce impacts on services.
      -  Clicking **OK** will start the upgrade immediately, and the upgrade cannot be canceled. Do not shut down or restart nodes during the upgrade.

#. In the displayed **Upgrade** dialog box, read the information and click **OK**. Note that the cluster cannot be rolled back after the upgrade.

#. Back to the cluster list, you can see that the cluster status is **Upgrading**. Wait until the upgrade is completed.

   After the upgrade is successful, you can view the cluster status and version on the cluster list or cluster details page.
