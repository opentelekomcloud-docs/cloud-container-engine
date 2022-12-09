:original_name: cce_01_0063.html

.. _cce_01_0063:

Managing Node Scaling Policies
==============================

Scenario
--------

After a node scaling policy is created, you can delete, edit, disable, enable, or clone the policy.

Viewing a Node Scaling Policy
-----------------------------

You can view the associated node pool, rules, and scaling history of a node scaling policy and rectify faults according to the error information displayed.

#. Log in to the CCE console. In the navigation pane, choose **Auto Scaling**. On the **Node Scaling** tab page, click |image1| in front of the policy to be viewed.
#. In the expanded area, the **Associated Node Pool**, **Execution Rules**, and **Scaling Records** tab pages are displayed. If the policy is abnormal, locate and rectify the fault based on the error information.

   .. note::

      You can also enable or disable auto scaling in **Node Pools**. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Node Pools**, and click **Edit** in the upper right corner of the node pool to be operated. In the **Edit Node Pool** dialog box displayed, you can enable **Autoscaler** and set the limits of the number of nodes.

Deleting a Node Scaling Policy
------------------------------

#. Log in to the CCE console. In the navigation pane, choose **Auto Scaling**. On the **Node Scaling** tab page, click **Delete** in the **Operation** column of the policy to be deleted.
#. In the **Delete Node Policy** dialog box displayed, confirm whether to delete the policy.
#. Enter **DELETE** in the text box.
#. Click **OK** to delete the policy.

Editing a Node Scaling Policy
-----------------------------

#. Log in to the CCE console. In the navigation pane, choose **Auto Scaling**. On the **Node Scaling** tab page, click **Edit** in the **Operation** column of the policy.
#. On the **Edit Node Scaling Policy** page displayed, modify policy parameter values listed in :ref:`Table 1 <cce_01_0209__table18763092201>`.
#. After the configuration is complete, click **OK**.

Cloning a Node Scaling Policy
-----------------------------

#. Log in to the CCE console. In the navigation pane, choose **Auto Scaling**. On the **Node Scaling** tab page, click **More** > **Clone** in the **Operation** column of the policy.
#. On the **Create Node Scaling Policy** page displayed, certain parameters have been cloned. Add or modify other policy parameters based on service requirements.
#. Click **Create Now** to clone the policy. The cloned policy is displayed in the policy list on the **Node Scaling** tab page.

Enabling or Disabling a Node Scaling Policy
-------------------------------------------

#. Log in to the CCE console. In the navigation pane, choose **Auto Scaling**. On the **Node Scaling** tab page, click **More** > **Disable** or **Enable** in the **Operation** column of the policy.
#. In the dialog box displayed, confirm whether to disable or enable the node policy.
#. Click **Yes**. The policy status is displayed in the node scaling list.

.. |image1| image:: /_static/images/en-us_image_0254986677.png
