:original_name: cce_10_0063.html

.. _cce_10_0063:

Managing Node Scaling Policies
==============================

Scenario
--------

After a node scaling policy is created, you can delete, edit, disable, enable, or clone the policy.

Viewing a Node Scaling Policy
-----------------------------

You can view the associated node pool, rules, and scaling history of a node scaling policy and rectify faults according to the error information displayed.

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Nodes**.On the page displayed, click the **Node Pools** tab and then the name of the node pool for which an auto scaling policy has been created to view the node pool details.
#. On the node pool details page, click the **Auto Scaling** tab to view the auto scaling configuration and scaling records.

Deleting a Node Scaling Policy
------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Policies**. On the page displayed, click the **Node Scaling Policies** tab, locate the row containing the target policy and choose **More** > **Delete** in the **Operation** column.
#. In the **Delete Node Scaling Policy** dialog box displayed, confirm whether to delete the policy.
#. Click **Yes** to delete the policy.

Editing a Node Scaling Policy
-----------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Policies**. On the page displayed, click the **Node Scaling Policies** tab, locate the row containing the target policy and click **Edit** in the **Operation** column.
#. On the **Edit Node Scaling Policy** page displayed, configure policy parameters listed in :ref:`Table 2 <cce_10_0209__table18763092201>`.
#. After the configuration is complete, click **OK**.

Cloning a Node Scaling Policy
-----------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Policies**. On the page displayed, click the **Node Scaling Policies** tab, locate the row containing the target policy and choose **More** > **Clone** in the **Operation** column.
#. On the **Clone Node Scaling Policy** page displayed, certain parameters have been cloned. Add or modify other policy parameters based on service requirements.
#. Click **OK**.

Enabling or Disabling a Node Scaling Policy
-------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Policies**. On the page displayed, click the **Node Scaling Policies** tab, locate the row containing the target policy click **Disable** in the **Operation** column. If the policy is in the disabled state, click **Enable** in the **Operation** column.
#. In the dialog box displayed, confirm whether to disable or enable the node policy.
