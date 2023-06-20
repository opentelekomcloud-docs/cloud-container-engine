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

#. Log in to the CCE console and access the cluster console.
#. Choose **Node Scaling** in the navigation pane and click |image1| in front of the policy to be viewed.
#. In the expanded area, the **Associated Node Pools**, **Rules**, and **Scaling History** tab pages are displayed. If the policy is abnormal, locate and rectify the fault based on the error information.

   .. note::

      You can also disable or enable auto scaling on the **Node Pools** page.

      a. Log in to the CCE console and access the cluster console.
      b. In the navigation pane, choose **Nodes** and switch to the **Node Pools** tab page.
      c. Click **Edit** of the node pool to be operated. In the **Edit Node Pool** dialog box that is displayed, set the limits of the number of nodes.

Deleting a Node Scaling Policy
------------------------------

#. Log in to the CCE console and access the cluster console.
#. Choose **Node Scaling** in the navigation pane and choose **More** > **Delete** next to the policy to be deleted.
#. In the **Delete Node Scaling Policy** dialog box displayed, confirm whether to delete the policy.
#. Click **Yes** to delete the policy.

Editing a Node Scaling Policy
-----------------------------

#. Log in to the CCE console and access the cluster console.
#. Choose **Node Scaling** in the navigation pane and click **Edit** in the **Operation** column of the policy to be edited.
#. On the **Edit Node Scaling Policy** page displayed, modify policy parameter values listed in :ref:`Table 1 <cce_10_0209__table18763092201>`.
#. After the configuration is complete, click **OK**.

Cloning a Node Scaling Policy
-----------------------------

#. Log in to the CCE console and access the cluster console.
#. Choose **Node Scaling** in the navigation pane and choose **More** > **Clone** next to the policy to be cloned.
#. On the **Clone Node Scaling Policy** page displayed, certain parameters have been cloned. Add or modify other policy parameters based on service requirements.
#. Click **OK**.

Enabling or Disabling a Node Scaling Policy
-------------------------------------------

#. Log in to the CCE console and access the cluster console.
#. Choose **Node Scaling** in the navigation pane and click **Disable** in the **Operation** column of the policy to be disabled. If the policy is in the disabled state, click **Enable** in the **Operation** column of the policy.
#. In the dialog box displayed, confirm whether to disable or enable the node policy.

.. |image1| image:: /_static/images/en-us_image_0000001517743464.png
