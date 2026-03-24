:original_name: en-us_topic_0000002516078019.html

.. _en-us_topic_0000002516078019:

Configuring Alarms for Java Applications on APM
===============================================

Application Performance Management (APM) monitors and manages the performance and faults of cloud applications in real time. It helps O&M personnel detect performance bottlenecks and identify fault causes, ensuring user experience. To monitor a Java workload that is deployed in a CCE standard or Turbo cluster, simply enable APM probes. There is no need to modify code. The APM probes help identify errors and slow APIs, reproduce calling parameters, and detect system bottlenecks. This greatly improves the efficiency of diagnosing online problems.

APM allows you to configure application alarms to quickly learn about the service exceptions and rectify the faults to avoid service loss. APM provides recommended basic alarm templates. For example, you can detect the JVM memory pressure in advance through garbage collection (GC) alarms.

Configuring APM
---------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Workloads**. In the upper right corner of the displayed page, click **Create Workload**.
#. In the **Advanced Settings** area, click **APM Settings** and enable a probe. APM 2.0 is recommended.
#. Configure probe parameters. For details, see :ref:`Configuring APM <cce_10_0055>`.

   .. table:: **Table 1** APM 2.0 probe parameter settings

      +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter             | Example Value             | Description                                                                                                                                                                                                                                                                                            |
      +=======================+===========================+========================================================================================================================================================================================================================================================================================================+
      | Probe Version         | 2.5.2                     | Select a probe version as required based on the following considerations:                                                                                                                                                                                                                              |
      |                       |                           |                                                                                                                                                                                                                                                                                                        |
      |                       |                           | -  The **latest** probe version does not necessarily correspond to the most recent version. For details about the probe capabilities of different versions, see `JavaAgent Updates <https://docs.otc.t-systems.com/application-performance-management/umn/service_overview/javaagent_updates.html>`__. |
      |                       |                           | -  Probes support multiple CPU architectures (such as x86 and Arm). Select a probe version matching the architecture of the node where the workload is deployed. If the probe version does not contain **x86_64** or **aarch64**, CCE will align it with the node architecture.                        |
      +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Probe Upgrade Policy  | Auto upgrade upon restart | Select an option to define how a probe will be upgraded. It defaults to **Auto upgrade upon restart**. Options include:                                                                                                                                                                                |
      |                       |                           |                                                                                                                                                                                                                                                                                                        |
      |                       |                           | -  **Auto upgrade upon restart**: The probe image is always re-downloaded when the workload restarts.                                                                                                                                                                                                  |
      |                       |                           | -  **Manual upgrade upon restart**: If the probe image is available locally, the local image will be used. Otherwise, the image will be re-downloaded.                                                                                                                                                 |
      +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | APM Environment       | N/A                       | (Optional) Enter an environment name. APM will create an environment for the workload. APM environments help distinguish performance data across different deployment phases, such as production and testing, for classified management and precise analysis of application performance.               |
      +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | APM App               | default                   | Select an existing application from APM. APM applications help organize monitoring data by function module, allowing unified management and analysis.                                                                                                                                                  |
      +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Sub-app               | N/A                       | (Optional) Enter an APM sub-application name for more refined management.                                                                                                                                                                                                                              |
      |                       |                           |                                                                                                                                                                                                                                                                                                        |
      |                       |                           | If the sub-application already exists in APM, the workload will be associated with the sub-application. Otherwise, CCE will create the specified sub-application.                                                                                                                                      |
      +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Access Key            | **AK:** *xxx*             | Enter the APM AK/SK, which are used to obtain the permissions to report data. **CCE will retrieve the APM AK/SK for you.**                                                                                                                                                                             |
      |                       |                           |                                                                                                                                                                                                                                                                                                        |
      |                       | **SK:** *xxx*             |                                                                                                                                                                                                                                                                                                        |
      +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Configure other parameters and click **Create Workload**. After the workload status changes to **Running**, wait for about 3 minutes. The application data will be displayed on the APM console.

Configuring GC Alarms for Applications on APM
---------------------------------------------

You can create an alarm policy based on the GC alarm template on APM and apply the alarm policy to the CCE cluster. For details, see `Recommended Alarm Templates <https://docs.otc.t-systems.com/application-performance-management/umn/alarm_management/alarm_policies/recommended_alarm_templates.html>`__.

#. Log in to the APM console.
#. In the navigation pane, choose **Application Monitoring** > **Metrics**. In the navigation tree, click the target application to access its metric details page.
#. Switch to the **Alarm Templates** tab and click **Recommended Templates** to view the configured alarm templates.
#. Locate the GC template and click **Copy** in the **Operation** column to copy the recommended template to the template list. You can define the template name as needed.
#. Click **Yes**. The copied alarm template is displayed on the template list.
#. Click **Modify** in the **Operation** column and bind the node to make the copied template to take effect. In this example, select **All** for **Node Type**. New nodes will be automatically bound. This means if a workload added to a CCE cluster is connected to APM, the workload will be automatically bound to the alarm template.

   .. note::

      The nodes here refer to those in the application directory tree, not the nodes defined in Kubernetes clusters.

Viewing GC Alarms of an Application
-----------------------------------

#. Log in to the APM console.

#. In the navigation pane, choose **Alarm Center** > **Alarms**. Select an application from the application drop-down list and view alarms of the application.

   For details about how to configure the notification receiving mode (such as email or SMS), see `Alarm Notification <https://docs.otc.t-systems.com/application-performance-management/umn/alarm_management/alarm_notification.html>`__.
