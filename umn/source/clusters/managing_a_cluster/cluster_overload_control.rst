:original_name: cce_10_0602.html

.. _cce_10_0602:

Cluster Overload Control
========================

Scenario
--------

If overload control is enabled, concurrent requests are dynamically controlled based on the resource pressure of master nodes to keep them and the cluster available.

Notes and Constraints
---------------------

The cluster version must be 1.23 or later.

Enabling Overload Control
-------------------------

**Method 1: Enabling it when creating a cluster**

When creating a cluster of v1.23 or later, you can enable overload control during the cluster creation.

**Method 2: Enabling it in an existing cluster**

#. Log in to the CCE console and go to an existing cluster whose version is v1.23 or later.
#. On the cluster information page, view the master node information. If overload control is not enabled, a message is displayed. You can click **Enable** to enable the function.

Disabling Cluster Overload Control
----------------------------------

#. Log in to the CCE console and go to an existing cluster whose version is v1.23 or later.
#. On the **Cluster Information** page, click **Manage** in the upper right corner.
#. Set **support-overload** to **false** under **kube-apiserver**.
#. Click **OK**.
