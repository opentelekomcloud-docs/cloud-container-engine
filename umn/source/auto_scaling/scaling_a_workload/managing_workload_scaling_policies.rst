:original_name: cce_10_0083.html

.. _cce_10_0083:

Managing Workload Scaling Policies
==================================

Scenario
--------

After a workload scaling policy is created, you can update and delete the policy, as well as edit the YAML file.

Procedure
---------

You can view the rules, latest status, and events of a workload scaling policy and handle exceptions based on the error information displayed.

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Policies**. On the **Scaling Policies** tab page, click the **HPA Policies**/**CronHPA Policies** tab based on the scaling policy type.
#. Check the latest status, rules, and associated workloads of a scaling policy.

   .. note::

      You can also check a created scaling policy on the workload details page.

      a. Log in to the CCE console and click the cluster name to access the cluster console.
      b. In the navigation pane, choose **Workloads**. Click the workload name to view its details.
      c. On the workload details page, switch to the **Auto Scaling** tab page to obtain the scaling policies. You can also obtain the scaling policies you configured on the **Policies** page.

#. Manage scaling policies.

   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
   | Scaling Policy Type               | Operation                                                                                                                                         |
   +===================================+===================================================================================================================================================+
   | HPA                               | -  **View Events**: Check HPA policy events. If an error occurred, locate and rectify the fault based on the error message displayed on the page. |
   |                                   | -  **Edit YAML**: In the dialog box displayed, edit, copy, or download the YAML file.                                                             |
   |                                   | -  **Edit**: On the **Edit HPA Policy** page, configure policy parameters listed in :ref:`Table 1 <cce_10_0208__table8638121213265>`.             |
   |                                   | -  **Clone**: Duplicate an existing auto scaling policy and modify the parameter settings as required.                                            |
   |                                   | -  **Delete**: In the dialog box displayed, click **Yes**.                                                                                        |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
   | CronHPA                           | -  **View YAML**: In the dialog box displayed, copy or download the YAML file but you are not allowed to modify it.                               |
   |                                   | -  **Delete**: In the dialog box displayed, click **Yes**.                                                                                        |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------+
