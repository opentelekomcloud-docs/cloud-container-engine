:original_name: cce_10_0055.html

.. _cce_10_0055:

Configuring APM Settings
========================

`Application Performance Management (APM) <https://docs.otc.t-systems.com/application-performance-management/umn/service_overview/index.html>`__ monitors cloud application performance and faults in real time. This enables O&M personnel to quickly identify issues and pinpoint root causes, ensuring optimal user experience. To monitor a Java workload that is deployed in a CCE standard or Turbo cluster, simply enable APM probes. There is no need to modify code. The APM probes help identify errors and slow APIs, reproduce calling parameters, and detect system bottlenecks. This greatly improves the efficiency of diagnosing online problems. :ref:`Figure 1 <cce_10_0055__fig2014203013517>` illustrates the operational process of an APM probe. This section describes how to configure APM settings for a workload using the console.

.. _cce_10_0055__fig2014203013517:

.. figure:: /_static/images/en-us_image_0000002662009439.png
   :alt: **Figure 1** APM probe workflow

   **Figure 1** APM probe workflow

Prerequisites
-------------

You have enabled APM. For details, see `Enabling APM 2.0 <https://docs.otc.t-systems.com/application-performance-management/umn/getting_started/enabling_apm.html>`__.

Notes and Constraints
---------------------

-  **APM is only available in certain regions**, which vary by APM version. For details, see the APM console. **If the APM service is unavailable in the current region, APM settings on CCE will also be unavailable.**
-  Only Deployments, StatefulSets, and DaemonSets support APM settings.

Configuring APM Settings Configuring APM Settings
-------------------------------------------------

When creating a Java workload, take the following steps to enable an APM probe to monitor the workload.

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Workloads**. In the upper right corner of the displayed page, click **Create Workload**.
#. In the **Advanced Settings** area, click **APM Settings** and enable a probe. By default, the probe is disabled. You can choose **APM 2.0** as needed.

   .. note::

      After an APM probe is enabled, CCE automatically applies the following configurations to send Java application data to APM for more accurate problem analysis and root cause identification:

      -  Creates an init container (**init-javaagent** for APM 2.0) to initialize the probe, allocating 0.25 CPU cores and 250 MiB of memory.
      -  Adds the **PAAS_MONITORING_GROUP**, **JAVA_TOOL_OPTIONS**, and **PAAS_CLUSTER_ID** environment variables to all service containers in the workload.

      -  Mounts a local storage volume (**paas-apm2** for APM 2.0) to all service containers in the workload.

#. Configure probe-related parameters.

   -  APM 2.0

      .. table:: **Table 1** APM 2.0 probe parameters

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
         | Access Key            | **AK:** *xxx*             | Enter the APM AK/SK, which are used to obtain the permissions to report data. **CCE will retrieve the APM AK/SK.**                                                                                                                                                                                     |
         |                       |                           |                                                                                                                                                                                                                                                                                                        |
         |                       | **SK:** *xxx*             |                                                                                                                                                                                                                                                                                                        |
         +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Configure other parameters and click **Create Workload**. After the workload status changes to **Running**, wait for about 3 minutes. The application data will be displayed on the APM console. Now you can log in to the APM console and optimize application performance through topologies and tracing. For details, see `Application Metric Monitoring <https://docs.otc.t-systems.com/application-performance-management/umn/application_metric_monitoring/index.html>`__.

Configuring APM Settings After Workload Deployment
--------------------------------------------------

Take the following steps to enable an APM probe for an existing Java workload to monitor the workload.

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Workloads**. In the right pane, locate the row containing the target workload on the corresponding tab. Click the name of the workload.
#. On the workload details page, click the **APM Settings** tab. In the **Probe** area, click **Edit** on the right.
#. By default, the probe is disabled. You can choose **APM 2.0** as needed.

   .. note::

      After an APM probe is enabled, CCE automatically applies the following configurations to send Java application data to APM for more accurate problem analysis and root cause identification:

      -  Creates an init container (**init-javaagent** for APM 2.0) to initialize the probe, allocating 0.25 CPU cores and 250 MiB of memory.
      -  Adds the **PAAS_MONITORING_GROUP**, **JAVA_TOOL_OPTIONS**, and **PAAS_CLUSTER_ID** environment variables to all service containers in the workload.

      -  Mounts a local storage volume (**paas-apm2** for APM 2.0) to all service containers in the workload.

#. Configure probe-related parameters.

   -  APM 2.0

      .. table:: **Table 2** APM 2.0 probe parameters

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
         | Access Key            | **AK:** *xxx*             | Enter the APM AK/SK, which are used to obtain the permissions to report data. **CCE will retrieve the APM AK/SK.**                                                                                                                                                                                     |
         |                       |                           |                                                                                                                                                                                                                                                                                                        |
         |                       | **SK:** *xxx*             |                                                                                                                                                                                                                                                                                                        |
         +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. After completing the configuration, click **OK** in the lower right corner. After the workload status changes to **Running**, wait for about 3 minutes. The application data will be displayed on the APM console. Now you can log in to the APM console and optimize application performance through topologies and tracing. For details, see `Application Metric Monitoring <https://docs.otc.t-systems.com/application-performance-management/umn/application_metric_monitoring/index.html>`__.

Modifying APM Settings
----------------------

After enabling an APM probe, you can modify the probe settings.

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Workloads**. In the right pane, locate the row containing the target workload on the corresponding tab. Click the name of the workload.
#. On the workload details page, click the **APM Settings** tab. In the **Probe** area, click **Edit** on the right.
#. Modify the APM probe parameters as required.

   -  APM 2.0

      .. table:: **Table 3** APM 2.0 probe parameters

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
         | Access Key            | **AK:** *xxx*             | Enter the APM AK/SK, which are used to obtain the permissions to report data. **CCE will retrieve the APM AK/SK.**                                                                                                                                                                                     |
         |                       |                           |                                                                                                                                                                                                                                                                                                        |
         |                       | **SK:** *xxx*             |                                                                                                                                                                                                                                                                                                        |
         +-----------------------+---------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. After completing the configuration, click **OK** in the lower right corner. After the workload status changes to **Running**, APM service will be updated, which takes about 3 minutes.
