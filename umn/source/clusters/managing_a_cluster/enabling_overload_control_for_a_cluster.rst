:original_name: cce_10_0602.html

.. _cce_10_0602:

Enabling Overload Control for a Cluster
=======================================

Scenario
--------

After overload control is enabled, the number of simultaneous requests is dynamically regulated according to the resource pressure on the master nodes. This ensures that both the nodes and the cluster remain accessible.

Notes and Constraints
---------------------

The cluster version must be 1.23 or later.

Enabling Overload Control
-------------------------

**Method 1: Enabling it when creating a cluster**

When creating a cluster of v1.23 or later, you can enable overload control during the cluster creation.

**Method 2: Enabling it in an existing cluster**

#. Log in to the CCE console and click the name of an existing cluster whose version is v1.23 or later.
#. On the **Overview** page, check the master node information. If overload control is not enabled, a message will be displayed. You can click **Enable** to enable the function.

Disabling Cluster Overload Control
----------------------------------

#. Log in to the CCE console and click the name of an existing cluster whose version is v1.23 or later.
#. In the navigation pane, choose **Settings**.
#. On the **Cluster Access** tab page, disable overload control.
#. Click **OK**.
