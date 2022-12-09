:original_name: cce_01_0145.html

.. _cce_01_0145:

Uploading a Chart
=================

Scenario
--------

Upload a chart to **Charts** > **Uploaded Charts** for subsequent workload creation.

Procedure
---------

#. Log in to the CCE console. In the navigation pane, choose **Charts** > **Uploaded Charts** and click **Upload Chart**.
#. Click **Select File**, select the chart to be uploaded, and click **Upload**.

   .. note::

      When you upload a chart, the naming rule of the OBS bucket is changed from cce-charts-{*region*}-{**domain_name**} to cce-charts-{*region*}-{**domain_id**}. In the old naming rule, the system converts the **domain_name** value into a Base64 string and uses the first 63 characters. If you cannot find the chart in the OBS bucket with the new name, search for the bucket with the old name.

Related Operations
------------------

After a chart is created, you can perform operations listed in :ref:`Table 1 <cce_01_0145__t84ae87674877489b975382f30a71dfab>` on the **Uploaded Charts** page.

.. _cce_01_0145__t84ae87674877489b975382f30a71dfab:

.. table:: **Table 1** Related operations

   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------+
   | Operation                         | Description                                                                                                                                  |
   +===================================+==============================================================================================================================================+
   | Installing a chart                | Click **Install Chart** to install the chart for creating workloads. For details, see :ref:`Creating a Workload from a Chart <cce_01_0146>`. |
   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------+
   | Updating a chart                  | The chart content will be updated while the chart version remains unchanged. The procedure is similar to that of uploading a chart.          |
   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------+
   | Downloading a chart               | Click **More** > **Download** to download the chart to the local host.                                                                       |
   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------+
   | Deleting a chart                  | Click **More** > **Delete** to delete the installed chart.                                                                                   |
   |                                   |                                                                                                                                              |
   |                                   | Deleted charts cannot be restored. Exercise caution when performing this operation.                                                          |
   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------+
