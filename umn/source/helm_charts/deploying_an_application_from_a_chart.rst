:original_name: cce_10_0146.html

.. _cce_10_0146:

Deploying an Application from a Chart
=====================================

On the CCE console, you can upload a Helm chart package, deploy it, and manage the deployed pods.

Notes and Constraints
---------------------

-  The number of charts that can be uploaded by a single user is limited. The value displayed on the console of each region is the allowed quantity.
-  A chart with multiple versions consumes the same portion as chart quotas.
-  Users with chart operation permissions can perform multiple operations on clusters. Therefore, exercise caution when assigning users the chart lifecycle management permissions, including uploading charts and creating, deleting, and updating chart releases.

Chart Specifications
--------------------

The Redis workload is used as an example to illustrate the chart specifications.

-  **Naming Requirement**

   A chart package is named in the format of **{name}-{version}**.tgz, where **{version}** indicates the version number in the format of *Major version number*.\ *Minor version number*.\ *Revision number*, for example, **redis-0.4.2.tgz**.

   .. note::

      The chart name {name} can contain a maximum of 64 characters.

      The version number must comply with the `semantic versioning <https://semver.org/>`__ rules.

      -  The main and minor version numbers are mandatory, and the revision number is optional.
      -  The major and minor version numbers and revision number must be integers, greater than or equal to 0, and less than or equal to 99.

-  **Directory Structure**

   The directory structure of a chart is as follows:

   .. code-block::

      redis/
        templates/
        values.yaml
        README.md
        Chart.yaml
        .helmignore

   As listed in :ref:`Table 1 <cce_10_0146__tb7d789a3467e4fe9b4385a51f3460321>`, the parameters marked with \* are mandatory.

   .. _cce_10_0146__tb7d789a3467e4fe9b4385a51f3460321:

   .. table:: **Table 1** Parameters in the directory structure of a chart

      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                         |
      +===================================+=====================================================================================================================================================================================================================================================================================================================================================================================================================+
      | \* templates                      | Stores all templates.                                                                                                                                                                                                                                                                                                                                                                                               |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* values.yaml                    | Describes configuration parameters required by templates.                                                                                                                                                                                                                                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                   | .. important::                                                                                                                                                                                                                                                                                                                                                                                                      |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                   |    NOTICE:                                                                                                                                                                                                                                                                                                                                                                                                          |
      |                                   |    Make sure that the image address set in the **values.yaml** file is the same as the image address in the container image repository. Otherwise, an exception occurs when you create a workload, and the system displays a message indicating that the image fails to be pulled.                                                                                                                                  |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                   |    To obtain the image address, perform the following operations: Log in to the CCE console. In the navigation pane, choose **Image Repository** to access the SWR console. Choose **My Images** > **Private Images** and click the name of the uploaded image. On the **Image Tags** tab, obtain the image address from the pull command. You can click to copy the command in the **Image Pull Command** column.  |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | README.md                         | A markdown file, including:                                                                                                                                                                                                                                                                                                                                                                                         |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                   | -  The workload or services provided by the chart.                                                                                                                                                                                                                                                                                                                                                                  |
      |                                   | -  Prerequisites for running the chart.                                                                                                                                                                                                                                                                                                                                                                             |
      |                                   | -  Configurations in the **values.yaml** file.                                                                                                                                                                                                                                                                                                                                                                      |
      |                                   | -  Information about chart installation and configuration.                                                                                                                                                                                                                                                                                                                                                          |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Chart.yaml                     | Basic information about the chart.                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                   | Note: The API version of Helm v3 is switched from v1 to v2.                                                                                                                                                                                                                                                                                                                                                         |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | .helmignore                       | Files or data that does not need to read templates during workload installation.                                                                                                                                                                                                                                                                                                                                    |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Uploading a Chart
-----------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **App Templates**. Then click **Upload Chart** in the upper right corner.
#. Click **Select File**, select the chart to be uploaded, and click **Upload**.

   .. note::

      When you upload a chart, the naming rule of the OBS bucket is changed from cce-charts-{*region*}-{**domain_name**} to cce-charts-{*region*}-{**domain_id**}. In the old naming rule, the system converts the **domain_name** value into a Base64 string and uses the first 63 characters. If you cannot find the chart in the OBS bucket with the new name, search for the bucket with the old name.

Creating a Release
------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **App Templates**. On the **My Charts** tab, click **Install** under the target chart.

#. Configure the parameters by referring to :ref:`Table 2 <cce_10_0146__t26bc1c499f114b5185e5edcf61e44d95>`.

   .. _cce_10_0146__t26bc1c499f114b5185e5edcf61e44d95:

   .. table:: **Table 2** Parameters for installing a chart

      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                               |
      +===================================+===========================================================================================================================================================================================+
      | Instance                          | Unique name of the chart release.                                                                                                                                                         |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Namespace                         | Namespace to which the workload will be deployed.                                                                                                                                         |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Select Version                    | Version of a chart.                                                                                                                                                                       |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Configuration File                | You can import and replace the **values.yaml** file or directly edit the chart parameters online.                                                                                         |
      |                                   |                                                                                                                                                                                           |
      |                                   | .. note::                                                                                                                                                                                 |
      |                                   |                                                                                                                                                                                           |
      |                                   |    An imported **values.yaml** file must comply with YAML specifications, that is, KEY:VALUE format. The fields in the file are not restricted.                                           |
      |                                   |                                                                                                                                                                                           |
      |                                   |    The key value of the imported values.yaml must be the same as that of the selected chart package. Otherwise, the values.yaml does not take effect. That is, the key cannot be changed. |
      |                                   |                                                                                                                                                                                           |
      |                                   | a. Click **Select File**.                                                                                                                                                                 |
      |                                   | b. Select the corresponding **values.yaml** file and click **Open**.                                                                                                                      |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Install**.

   On the **Releases** tab, you can view the installation status of the release.

Upgrading an Application Deployed Using a Chart
-----------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **App Templates**. On the right, click the **Releases** tab.
#. Locate the target release, click **Upgrade** in the **Operation** column, and then configure the parameters.
#. Select a chart version.
#. Follow the prompts to modify the chart parameters. Confirm the modification and click **Upgrade**.
#. If the execution status is **Upgraded**, the release has been upgraded.

Rolling Back an Application Deployed Using a Chart
--------------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **App Templates**. On the right, click the **Releases** tab.

#. Choose **More** > **Roll Back** to roll back the release, select the target release version, and roll back the release.

   In the release list, if the status is **Rollback successful**, the release has been rolled back.

Uninstalling an Application Deployed Using a Chart
--------------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **App Templates**. On the right, click the **Releases** tab.
#. Click **More** > **Uninstall** next to the release to be uninstalled, and click **Yes**. Exercise caution when performing this operation because releases cannot be restored after being uninstalled.
