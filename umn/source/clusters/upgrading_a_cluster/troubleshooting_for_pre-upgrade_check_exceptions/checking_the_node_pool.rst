:original_name: cce_10_0436.html

.. _cce_10_0436:

Checking the Node Pool
======================

Check Item
----------

Check the following aspects:

-  Check the node status.
-  Check whether the auto scaling function of the node pool is disabled.

Solution
--------

-  **Scenario 1: The node pool status is abnormal.**

   Log in to the CCE console, go to the target cluster and choose **Nodes**. On the displayed page, click **Node Pools** tab and check the node pool status. If the node pool is being scaled, wait until the scaling is complete, and disable the auto scaling function by referring to :ref:`Scenario 2 <cce_10_0436__li2791152121810>`.

-  .. _cce_10_0436__li2791152121810:

   **Scenario 2: The auto scaling function of the node pool is enabled.**

   **Solution 1 (Recommended)**

   Log in to the CCE console and go to the target cluster. Choose **O&M** > **Add-ons** and uninstall the autoscaler add-on.

   .. note::

      Before uninstalling the autoscaler add-on, click **Upgrade** to back up the configuration so that the add-on configuration can be restored during reinstallation.

      Before uninstalling the autoscaler add-on, choose **O&M** > **Node Scaling** and back up the current scaling policies so that they can be restored during reinstallation. These policies will be deleted when the autoscaler add-on is uninstalled.

   Obtain and back up the node scaling policy by clicking **Edit**.

   **Solution 2**

   If you do not want to uninstall the autoscaler add-on, log in to the CCE console and access the cluster detail page. Choose **Nodes** in the navigation pane. On the displayed page, click the **Node Pools** tab and click **Edit** of the corresponding node pool to disable the auto scaling function.

   .. note::

      Before disabling the auto scaling function, back up the autoscaling configuration so that the configuration can be restored when the function is enabled.
