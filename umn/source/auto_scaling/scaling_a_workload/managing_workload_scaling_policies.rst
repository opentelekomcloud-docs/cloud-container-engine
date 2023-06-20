:original_name: cce_10_0083.html

.. _cce_10_0083:

Managing Workload Scaling Policies
==================================

Scenario
--------

After an HPA policy is created, you can update, clone, edit, and delete the policy, as well as edit the YAML file.

Checking an HPA Policy
----------------------

You can view the rules, status, and events of an HPA policy and handle exceptions based on the error information displayed.

#. Log in to the CCE console and access the cluster console.
#. In the navigation pane, choose **Workload Scaling**. On the **HPA Policies** tab page, click |image1| next to the target HPA policy.
#. In the expanded area, you can view the **Rules**, **Status**, and **Events** tab pages. If the policy is abnormal, locate and rectify the fault based on the error information.

   .. note::

      You can also view the created HPA policy on the workload details page.

      a. Log in to the CCE console and access the cluster console.
      b. In the navigation pane, choose **Workloads**. Click the workload name to view its details.
      c. On the workload details page, swich to the **Auto Scaling** tab page to view the HPA policies. You can also view the scaling policies you configured in **Workload Scaling**.

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

#. Log in to the CCE console and access the cluster console.
#. In the navigation pane, choose **Workload Scaling**. Click **Update** in the **Operation** column of the target policy.
#. On the **Update HPA Policy** page displayed, set the policy parameters listed in :ref:`Table 1 <cce_10_0208__table8638121213265>`.
#. Click **Update**.

Editing the YAML File (HPA Policy)
----------------------------------

#. Log in to the CCE console and access the cluster console.
#. In the navigation pane, choose **Workload Scaling**. Click **More > Edit YAML** in the **Operation** column of the target HPA policy.
#. In the **Edit YAML** dialog box displayed, edit or download the YAML file.
#. Click the close button in the upper right corner.

Deleting an HPA Policy
----------------------

#. Log in to the CCE console and access the cluster console.
#. In the navigation pane, choose **Workload Scaling**. Click **Delete** in the **Operation** column of the target policy.
#. In the dialog box displayed, click **Yes**.

.. |image1| image:: /_static/images/en-us_image_0000001568902521.png
