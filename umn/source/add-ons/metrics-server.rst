:original_name: cce_01_0205.html

.. _cce_01_0205:

metrics-server
==============

From version 1.8 onwards, Kubernetes provides resource usage metrics, such as the container CPU and memory usage, through the Metrics API. These metrics can be directly accessed by users (for example, by using the **kubectl top** command) or used by controllers (for example, Horizontal Pod Autoscaler) in a cluster for decision-making. The specific component is metrics-server, which is used to substitute for heapster for providing the similar functions. heapster has been gradually abandoned since v1.11.

metrics-server is an aggregator for monitoring data of core cluster resources. You can quickly install this add-on on the CCE console.

After metrics-server is installed, you can create an HPA policy on the **Workload Scaling** tab page of the **Auto Scaling** page. For details, see :ref:`Creating an HPA Policy for Workload Auto Scaling <cce_01_0208>`.

The official community project and documentation are available at https://github.com/kubernetes-sigs/metrics-server.

Notes and Constraints
---------------------

This add-on can be installed only in CCE clusters of v1.13 or later.

.. _cce_01_0205__section1962241123816:

Installing the Add-on
---------------------

#. Log in to the CCE console. In the navigation pane, choose **Add-ons**. On the **Add-on Marketplace** tab page, click **Install Add-on** under **metrics-server**.

#. On the **Install Add-on** page, select the cluster and the add-on version, and click **Next: Configuration**.

#. Select **Single** or **HA** for **Add-on Specifications**, and click **Install**.

   After the add-on is installed, click **Go Back to Previous Page**. On the **Add-on Instance** tab page, select the corresponding cluster to view the running instance. This indicates that the add-on has been installed on each node in the cluster.

Upgrading the Add-on
--------------------

#. Log in to the CCE console. In the navigation pane, choose **Add-ons**. On the **Add-on Instance** tab page, click **Upgrade** under **metrics-server**.

   .. note::

      -  If the **Upgrade** button is not available, the current add-on is already up-to-date and no upgrade is required.
      -  During the upgrade, the metrics-server add-on of the original version on cluster nodes will be discarded, and the add-on of the target version will be installed.

#. On the **Basic Information** page, select the add-on version and click **Next**.
#. Set the parameters by referring to the parameter description in :ref:`Installing the Add-on <cce_01_0205__section1962241123816>` and click **Upgrade**.

Uninstalling the Add-on
-----------------------

#. Log in to the CCE console. In the navigation pane, choose **Add-ons**. On the **Add-on Instance** tab page, click **Uninstall** under **metrics-server**.
#. In the dialog box displayed, click **Yes** to uninstall the add-on.
