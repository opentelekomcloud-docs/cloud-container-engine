:original_name: cce_01_0083.html

.. _cce_01_0083:

Managing Workload Scaling Policies
==================================

Scenario
--------

After an HPA policy is created, you can update, clone, edit, and delete the policy, as well as edit the YAML file.

Checking an HPA Policy
----------------------

You can view the rules, status, and events of an HPA policy and handle exceptions based on the error information displayed.

#. Log in to the CCE console. In the navigation pane, choose **Auto Scaling**. On the **Workload Scaling** tab page, click |image1| in front of the target policy.
#. In the expanded area, you can view the **Rules**, **Status**, and **Events** tab pages. If the policy is abnormal, locate and rectify the fault based on the error information.

   .. note::

      You can also view the created HPA policy on the workload details page. Log in to the CCE console, choose **Workloads > Deployments** or **Workloads > StatefulSets** in the navigation pane, and choose **More** > **Scaling** in the **Operation** column. On the workload details page, click the **Scaling** tab. You can see the **Auto Scaling-HPA** pane, as well as the HPA policy you have configured on the **Auto Scaling** page.

   .. table:: **Table 1** Event types and names

      +------------+------------------------------+----------------------------------------------+
      | Event Type | Event Name                   | Description                                  |
      +============+==============================+==============================================+
      | Normal     | SuccessfulRescale            | The scaling is performed successfully.       |
      +------------+------------------------------+----------------------------------------------+
      | Abnormal   | InvalidTargetRange           | Invalid target range.                        |
      +------------+------------------------------+----------------------------------------------+
      |            | InvalidSelector              | Invalid selector.                            |
      +------------+------------------------------+----------------------------------------------+
      |            | FailedGetObjectMetric        | Objects fail to be obtained.                 |
      +------------+------------------------------+----------------------------------------------+
      |            | FailedGetPodsMetric          | Pods fail to be obtained.                    |
      +------------+------------------------------+----------------------------------------------+
      |            | FailedGetResourceMetric      | Resources fail to be obtained.               |
      +------------+------------------------------+----------------------------------------------+
      |            | FailedGetExternalMetric      | External metrics fail to be obtained.        |
      +------------+------------------------------+----------------------------------------------+
      |            | InvalidMetricSourceType      | Invalid metric source type.                  |
      +------------+------------------------------+----------------------------------------------+
      |            | FailedConvertHPA             | HPA conversion failed.                       |
      +------------+------------------------------+----------------------------------------------+
      |            | FailedGetScale               | The scale fails to be obtained.              |
      +------------+------------------------------+----------------------------------------------+
      |            | FailedComputeMetricsReplicas | Failed to calculate metric-defined replicas. |
      +------------+------------------------------+----------------------------------------------+
      |            | FailedGetScaleWindow         | Failed to obtain ScaleWindow.                |
      +------------+------------------------------+----------------------------------------------+
      |            | FailedRescale                | Failed to scale the service.                 |
      +------------+------------------------------+----------------------------------------------+

Updating an HPA Policy
----------------------

An HPA policy is used as an example.

#. Log in to the CCE console. In the navigation pane, choose **Auto Scaling**. On the **Workload Scaling** tab page, click **Update** in the **Operation** column of the policy to be updated.
#. On the **Update HPA Policy** page displayed, set the policy parameters listed in :ref:`Table 1 <cce_01_0208__table8638121213265>`.
#. Click **Update**.

Cloning an HPA Policy
---------------------

#. Log in to the CCE console. In the navigation pane, choose **Auto Scaling**. On the **Workload Scaling** tab page, click **Clone** in the **Operation** column of the target policy.
#. For example, for an HPA policy, on the **Create HPA Policy** page, you can view that parameters such as **Pod Range**, **Cooldown Period**, and **Rules** have been cloned. Add or modify other policy parameters as needed.
#. Click **Create** to complete policy cloning. On the **Workload Scaling** tab page, you can view the cloned policy in the policy list.

Editing the YAML File (HPA Policy)
----------------------------------

#. Log in to the CCE console. In the navigation pane, choose **Auto Scaling**. On the **Workload Scaling** tab page, choose **More** > **Edit YAML** in the **Operation** column of the target policy.
#. In the **Edit YAML** dialog box displayed, edit or download the YAML file.
#. Click the close button in the upper right corner.

Deleting an HPA Policy
----------------------

#. Log in to the CCE console. In the navigation pane, choose **Auto Scaling**. On the **Workload Scaling** tab page, choose **More** > **Delete** in the **Operation** column of the target policy.
#. In the **Delete HPA Policy** dialog box displayed, confirm whether to delete the HPA policy.
#. Click **Yes** to delete the policy.

.. |image1| image:: /_static/images/en-us_image_0254985211.png
