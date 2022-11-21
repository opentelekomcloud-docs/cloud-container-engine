:original_name: cce_02_0271.html

.. _cce_02_0271:

How to Obtain Parameters in the API URI
=======================================

project_id
----------

#. Log in to the CCE console, click the username in the upper right corner, and select **My Credentials**.

#. On the **Projects** tab page, obtain the project ID of the corresponding region.


   .. figure:: /_static/images/en-us_image_0203982493.png
      :alt: **Figure 1** Viewing project IDs

      **Figure 1** Viewing project IDs

cluster_id
----------

#. Log in to the CCE console, and choose **Resource Management** > **Clusters** from the navigation pane.

#. Click the name of the created cluster. The cluster details page is displayed. Obtain the cluster ID.


   .. figure:: /_static/images/en-us_image_0233732613.png
      :alt: **Figure 2** Obtaining the cluster ID

      **Figure 2** Obtaining the cluster ID

node_id
-------

#. Log in to the CCE console, and choose **Resource Management** > **Nodes** from the navigation pane.

#. Click the node name to go to the node details page and obtain the node ID.


   .. figure:: /_static/images/en-us_image_0279177365.png
      :alt: **Figure 3** Obtaining the node ID

      **Figure 3** Obtaining the node ID

job_id
------

#. Log in to the CCE console, and choose **Resource Management** > **Clusters** or choose **Resource Management** > **Nodes** from the navigation pane.

#. For example, on the **Clusters** page, click the status of the cluster that is being created. The cluster creation job details page is displayed.

#. Obtain the job ID.

   If you are using Google Chrome, press **F12**. On the pane displayed on the right, click the **Network** tab. Enter **jobs** in the **Filter** text box to filter the job list. Select a job from the list on the left and click **Preview**. The UID field indicates the UID of the job.


   .. figure:: /_static/images/en-us_image_0203982464.png
      :alt: **Figure 4** Obtaining the job ID

      **Figure 4** Obtaining the job ID
