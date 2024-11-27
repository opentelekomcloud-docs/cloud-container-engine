:original_name: cce_10_0026.html

.. _cce_10_0026:

Viewing CTS Traces in the Trace List
====================================

Scenarios
---------

After you enable CTS and the management tracker is created, CTS starts recording operations on cloud resources. Cloud Trace Service (CTS) stores operation records (traces) generated in the last seven days.

.. note::

   These operation records are retained for seven days on the CTS console and are automatically deleted upon expiration. Manual deletion is not supported.

This section describes how to query or export operation records of the last seven days on the CTS console.

-  :ref:`Viewing Real-Time Traces in the Trace List of the New Edition <cce_10_0026__en-us_topic_0179639644_section6300091795238>`
-  :ref:`Viewing Real-Time Traces in the Trace List of the Old Edition <cce_10_0026__en-us_topic_0179639644_section19271975203>`

Constraints
-----------

-  You can only query operation records of the last seven days on the CTS console. To store operation records for longer than seven days, you must configure transfer to OBS or Log Tank Service (LTS) so that you can view them in OBS buckets or LTS log groups.
-  After performing operations on the cloud, you can query management traces on the CTS console one minute later.

.. _cce_10_0026__en-us_topic_0179639644_section6300091795238:

Viewing Real-Time Traces in the Trace List of the New Edition
-------------------------------------------------------------

#. Log in to the management console.
#. Click |image1| in the upper left corner and choose **Management & Deployment** > **Cloud Trace Service**. The CTS console is displayed.
#. Choose **Trace List** in the navigation pane on the left.
#. On the **Trace List** page, use advanced search to query traces. You can combine one or more filters.

   -  **Trace Name**: Enter a trace name.
   -  **Trace ID**: Enter a trace ID.
   -  **Resource Name**: Enter a resource name. If the cloud resource involved in the trace does not have a resource name or the corresponding API operation does not involve the resource name parameter, leave this field empty.
   -  **Resource ID**: Enter a resource ID. Leave this field empty if the resource has no resource ID or if resource creation failed.
   -  **Trace Source**: Select a cloud service name from the drop-down list.
   -  **Resource Type**: Select a resource type from the drop-down list.
   -  **Operator**: Select one or more operators from the drop-down list.
   -  **Trace Status**: Select **normal**, **warning**, or **incident**.

      -  **normal**: The operation succeeded.
      -  **warning**: The operation failed.
      -  **incident**: The operation caused a fault that is more serious than the operation failure, for example, causing other faults.

   -  Time range: Select **Last 1 hour**, **Last 1 day**, or **Last 1 week**, or specify a custom time range within the last seven days.

#. On the **Trace List** page, you can also export and refresh the trace list, and customize columns to display.

   -  Enter any keyword in the search box and press **Enter** to filter desired traces.
   -  Click **Export** to export all traces in the query result as an .xlsx file. The file can contain up to 5,000 records.
   -  Click |image2| to view the latest information about traces.
   -  Click |image3| to customize the information to be displayed in the trace list. If **Auto wrapping** is enabled (|image4|), excess text will move down to the next line; otherwise, the text will be truncated. By default, this function is disabled.

#. For details about key fields in the trace structure, see and section "Trace References" > "Example Traces".
#. (Optional) On the **Trace List** page of the new edition, click **Go to Old Edition** in the upper right corner to switch to the **Trace List** page of the old edition.

.. _cce_10_0026__en-us_topic_0179639644_section19271975203:

Viewing Real-Time Traces in the Trace List of the Old Edition
-------------------------------------------------------------

#. Log in to the management console.

#. Click |image5| in the upper left corner and choose **Management & Deployment** > **Cloud Trace Service**. The CTS console is displayed.

#. Choose **Trace List** in the navigation pane on the left.

#. Each time you log in to the CTS console, the new edition is displayed by default. Click **Go to Old Edition** in the upper right corner to switch to the trace list of the old edition.

#. Set filters to search for your desired traces, as shown in :ref:`Figure 1 <cce_10_0026__en-us_topic_0179639644_fig139361441134311>`. The following filters are available.

   .. _cce_10_0026__en-us_topic_0179639644_fig139361441134311:

   .. figure:: /_static/images/en-us_image_0000001744598325.png
      :alt: **Figure 1** Filters

      **Figure 1** Filters

   -  **Trace Type**, **Trace Source**, **Resource Type**, and **Search By**: Select a filter from the drop-down list.

      -  If you select **Resource ID** for **Search By**, specify a resource ID.
      -  If you select **Trace name** for **Search By**, specify a trace name.
      -  If you select **Resource name** for **Search By**, specify a resource name.

   -  **Operator**: Select a user.
   -  **Trace Status**: Select **All trace statuses**, **Normal**, **Warning**, or **Incident**.
   -  Time range: Select **Last 1 hour**, **Last 1 day**, or **Last 1 week**, or specify a custom time range within the last seven days.
   -  Click **Export** to export all traces in the query result as a CSV file. The file can contain up to 5,000 records.

#. Click **Query**.

#. On the **Trace List** page, you can also export and refresh the trace list.

   -  Click **Export** to export all traces in the query result as a CSV file. The file can contain up to 5,000 records.
   -  Click |image6| to view the latest information about traces.

#. Click |image7| on the left of a trace to expand its details.

   |image8|

#. Click **View Trace** in the **Operation** column. The trace details are displayed.

   |image9|

#. For details about key fields in the trace structure, see section "Trace References" > "Trace Structure" and section "Trace References" > "Example Traces" in the *CTS User Guide*.

#. (Optional) On the **Trace List** page of the old edition, click **New Edition** in the upper right corner to switch to the **Trace List** page of the new edition.

.. |image1| image:: /_static/images/en-us_image_0000001187249376.png
.. |image2| image:: /_static/images/en-us_image_0000001667734001.png
.. |image3| image:: /_static/images/en-us_image_0000001619094530.png
.. |image4| image:: /_static/images/en-us_image_0000001667694873.png
.. |image5| image:: /_static/images/en-us_image_0000001696838310.png
.. |image6| image:: /_static/images/en-us_image_0000001696678850.png
.. |image7| image:: /_static/images/en-us_image_0000001744678489.jpg
.. |image8| image:: /_static/images/en-us_image_0000001942942816.png
.. |image9| image:: /_static/images/en-us_image_0000001758618249.png
