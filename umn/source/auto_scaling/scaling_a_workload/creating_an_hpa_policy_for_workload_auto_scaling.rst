:original_name: cce_01_0208.html

.. _cce_01_0208:

Creating an HPA Policy for Workload Auto Scaling
================================================

Horizontal Pod Autoscaling (HPA) in Kubernetes implements horizontal scaling of pods. In a CCE HPA policy, you can configure different cooldown time windows and scaling thresholds for different applications based on the Kubernetes HPA.

Prerequisites
-------------

The :ref:`metrics-server <cce_01_0205>` add-on has been installed. This add-on collects public metrics of kubelet in Kubernetes clusters, including the CPU usage and memory usage.

Notes and Constraints
---------------------

-  HPA policies can be created only for clusters of v1.13 or later.

-  Only one policy can be created for each workload. That is, if you have created an HPA policy, you cannot create other HPA policies for the workload. You can delete the created HPA policy and create a new one.

-  For clusters earlier than v1.19.10, if an HPA policy is used to scale out a workload with EVS volumes mounted, the existing pods cannot be read or written when a new pod is scheduled to another node.

   For clusters of v1.19.10 and later, if an HPA policy is used to scale out a workload with EVS volume mounted, a new pod cannot be started because EVS disks cannot be attached.

Procedure
---------

#. Log in to the CCE console. In the navigation pane, choose **Auto Scaling**. On the **Workload Scaling** tab page, click **Create HPA Policy**.

#. In the **Check Add-ons** step:

   -  If |image1| is displayed next to the add-on name, click **Install**, set add-on parameters as required, and click **Install** to install the add-on.
   -  If |image2| is displayed next to the add-on name, the add-on has been installed.

#. After the required add-ons have been installed, click **Next: Policy configuration**.

   .. note::

      If the add-ons have been installed, after you click **Create HPA Policy**, you will directly land on the second step to configure the policy. The first step (checking the add-ons) has been completed almost instantly.

#. Set policy parameters by referring to :ref:`Table 1 <cce_01_0208__table8638121213265>`.

   .. _cce_01_0208__table8638121213265:

   .. table:: **Table 1** HPA policy parameters

      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                               |
      +===================================+===========================================================================================================================================================================================================================+
      | Policy Name                       | Name of the policy to be created. Set this parameter as required.                                                                                                                                                         |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Cluster Name                      | Cluster to which the workload belongs.                                                                                                                                                                                    |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Namespace                         | Namespace to which the workload belongs.                                                                                                                                                                                  |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Associated Workload               | Workload with which the HPA policy is associated.                                                                                                                                                                         |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Pod Range                         | Minimum and maximum numbers of pods.                                                                                                                                                                                      |
      |                                   |                                                                                                                                                                                                                           |
      |                                   | When a policy is triggered, the workload pods are scaled within this range.                                                                                                                                               |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Cooldown Period                   | Interval between a scale-in and a scale-out. The unit is minute. **The interval cannot be shorter than 1 minute.**                                                                                                        |
      |                                   |                                                                                                                                                                                                                           |
      |                                   | **This parameter is available only for clusters of v1.15 and later. It is not supported in clusters of v1.13 or earlier.**                                                                                                |
      |                                   |                                                                                                                                                                                                                           |
      |                                   | This parameter indicates the interval between consecutive scaling operations. The cooldown period ensures that a scaling operation is initiated only when the previous one is completed and the system is running stably. |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Rules                             | Policy rules can be based on system metrics.                                                                                                                                                                              |
      |                                   |                                                                                                                                                                                                                           |
      |                                   | **System metrics**                                                                                                                                                                                                        |
      |                                   |                                                                                                                                                                                                                           |
      |                                   | -  **Metric**: You can select **CPU usage** or **Memory usage**.                                                                                                                                                          |
      |                                   |                                                                                                                                                                                                                           |
      |                                   |    .. note::                                                                                                                                                                                                              |
      |                                   |                                                                                                                                                                                                                           |
      |                                   |       Usage = CPUs or memory used by pods/Requested CPUs or memory.                                                                                                                                                       |
      |                                   |                                                                                                                                                                                                                           |
      |                                   | -  **Expected Value**: Enter the expected average resource usage.                                                                                                                                                         |
      |                                   |                                                                                                                                                                                                                           |
      |                                   |    This parameter indicates the expected value of the selected metric. The number of new pods required (rounded up) = Current metric value/Expected value x Number of current pods                                        |
      |                                   |                                                                                                                                                                                                                           |
      |                                   | -  **Threshold**: Enter the scaling thresholds.                                                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                           |
      |                                   |    If the metric value is greater than the scale-in threshold and less than the scale-out threshold, no scaling is triggered. **This parameter is supported only in clusters of v1.15 or later.**                         |
      |                                   |                                                                                                                                                                                                                           |
      |                                   | You can click **Add Rule** again to add more scaling policies.                                                                                                                                                            |
      |                                   |                                                                                                                                                                                                                           |
      |                                   | .. note::                                                                                                                                                                                                                 |
      |                                   |                                                                                                                                                                                                                           |
      |                                   |    When calculating the number of pods to be added or reduced, the HPA policy uses the maximum metrics values in the last 5 minutes.                                                                                      |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. After the configuration is complete, click **Create**. If the system displays a message indicating that the request to create workload policy \**\* is successfully submitted, click **Back to Workload Scaling**.

#. On the **Workload Scaling** tab page, you can view the newly created HPA policy.

.. |image1| image:: /_static/images/en-us_image_0259716601.png
.. |image2| image:: /_static/images/en-us_image_0259714782.png
