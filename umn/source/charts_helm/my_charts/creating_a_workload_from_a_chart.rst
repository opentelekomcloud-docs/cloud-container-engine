:original_name: cce_01_0146.html

.. _cce_01_0146:

Creating a Workload from a Chart
================================

Creating a Chart-based Workload
-------------------------------

#. Log in to the CCE console. In the navigation pane, choose **Charts** > **Uploaded Chart**.

#. In the list of uploaded charts, click **Install**.

#. Set the installation parameters listed in :ref:`Table 1 <cce_01_0146__t26bc1c499f114b5185e5edcf61e44d95>`. The parameters marked with an asterisk (*) are mandatory.

   .. _cce_01_0146__t26bc1c499f114b5185e5edcf61e44d95:

   .. table:: **Table 1** Installation parameters

      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                               |
      +===================================+===========================================================================================================================================================================================+
      | \* Release Name                   | Unique name of the chart release.                                                                                                                                                         |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Chart Version                  | Chart version by default.                                                                                                                                                                 |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Cluster                        | Cluster where the workload will be deployed.                                                                                                                                              |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Namespace                      | Namespace to which the workload will be deployed.                                                                                                                                         |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Advanced Settings                 | You can import and replace the **values.yaml** file or directly edit the chart parameters online.                                                                                         |
      |                                   |                                                                                                                                                                                           |
      |                                   | .. note::                                                                                                                                                                                 |
      |                                   |                                                                                                                                                                                           |
      |                                   |    An imported **values.yaml** file must comply with YAML specifications, that is, KEY:VALUE format. The fields in the file are not restricted.                                           |
      |                                   |                                                                                                                                                                                           |
      |                                   |    The key value of the imported values.yaml must be the same as that of the selected chart package. Otherwise, the values.yaml does not take effect. That is, the key cannot be changed. |
      |                                   |                                                                                                                                                                                           |
      |                                   | a. Click **Import Configuration File**.                                                                                                                                                   |
      |                                   | b. Select the corresponding **values.yaml** file and click **Open**.                                                                                                                      |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. After the configuration is complete, click **Next**.

#. Confirm the configuration and click **Submit**.

#. Click **Back to Release List** to view the running status of the chart-based workload (also called release), or click **View Release Details** to view details about the release.

Upgrading a Chart-based Workload
--------------------------------

#. Log in to the CCE console. In the navigation pane, choose **Charts** > **Uploaded Charts**. Click the **Template Instances** tab.
#. Click **Upgrade** in the row where the desired workload resides and set the parameters for the workload.
#. Select a chart version for **Chart Version**.
#. Follow the prompts to modify the chart parameters. Click **Upgrade**, and then click **Submit**.
#. Click **Back to Release List**. If the chart status changes to **Upgrade successful**, the workload is successfully upgraded.

Rolling Back a Chart-based Workload
-----------------------------------

#. Log in to the CCE console. In the navigation pane, choose **Charts** > **Uploaded Charts**. Click the **Template Instances** tab.

#. Click **More** > **Roll Back** for the workload to be rolled back, select the workload version, and click **Roll back** **to this version**.

   In the workload list, if the status is **Rollback successful**, the workload is rolled back successfully.

Uninstalling a Chart-based Workload
-----------------------------------

#. Log in to the CCE console. In the navigation pane, choose **Charts** > **Uploaded Charts**. Click the **Template Instances** tab.
#. Click **More** > **Uninstall** next to the release to be uninstalled, and click **Yes**. Exercise caution when performing this operation because releases cannot be restored after being uninstalled.
