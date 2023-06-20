:original_name: cce_10_0007.html

.. _cce_10_0007:

Managing Workloads and Jobs
===========================

Scenario
--------

After a workload is created, you can upgrade, monitor, roll back, or delete the workload, as well as edit its YAML file.

.. table:: **Table 1** Workload/Job management

   +------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Operation                                                                                      | Description                                                                                                                                                                                    |
   +================================================================================================+================================================================================================================================================================================================+
   | :ref:`Monitor <cce_10_0007__section7200124254011>`                                             | You can view the CPU and memory usage of workloads and pods on the CCE console.                                                                                                                |
   +------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`View Log <cce_10_0007__en-us_topic_0107283638_section51511928173817>`                    | You can view the logs of workloads.                                                                                                                                                            |
   +------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Upgrade <cce_10_0007__en-us_topic_0107283638_section17604174417381>`                     | You can replace images or image tags to quickly upgrade Deployments, StatefulSets, and DaemonSets without interrupting services.                                                               |
   +------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Edit YAML <cce_10_0007__en-us_topic_0107283638_section21669213390>`                      | You can modify and download the YAML files of Deployments, StatefulSets, DaemonSets, and pods on the CCE console. YAML files of jobs and cron jobs can only be viewed, copied, and downloaded. |
   +------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Roll Back <cce_10_0007__en-us_topic_0107283638_section13324541124815>`                   | Only Deployments can be rolled back.                                                                                                                                                           |
   +------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Redeploy <cce_10_0007__section132451237607>`                                             | You can redeploy a workload. After the workload is redeployed, all pods in the workload will be restarted.                                                                                     |
   +------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Enabling/Disabling the Upgrade <cce_10_0007__en-us_topic_0107283638_section12087915401>` | Only Deployments support this operation.                                                                                                                                                       |
   +------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Manage Label <cce_10_0007__en-us_topic_0107283638_section5931193015488>`                 | Labels are key-value pairs and can be attached to workloads for affinity and anti-affinity scheduling. Jobs and Cron Jobs do not support this operation.                                       |
   +------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Delete <cce_10_0007__en-us_topic_0107283638_section14423721191418>`                      | You can delete a workload or job that is no longer needed. Deleted workloads or jobs cannot be recovered.                                                                                      |
   +------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`View Events <cce_10_0007__en-us_topic_0107283638_section1947616516301>`                  | You can view event names, event types, number of occurrences, Kubernetes events, first occurrence time, and last occurrence time.                                                              |
   +------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Stop/Start                                                                                     | You can only start or stop a cron job.                                                                                                                                                         |
   +------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_10_0007__section7200124254011:

Monitoring a Workload
---------------------

You can view the CPU and memory usage of Deployments and pods on the CCE console to determine the resource specifications you may need. This section uses a Deployment as an example to describe how to monitor a workload.

#. Log in to the CCE console, go to an existing cluster, and choose **Workloads** in the navigation pane.
#. Click the **Deployments** tab and click **Monitor** of the target workload. On the page that is displayed, you can view CPU usage and memory usage of the workload.
#. Click the workload name. On the **Pods** tab page, click the **Monitor** of the target pod to view its CPU and memory usage.

.. _cce_10_0007__en-us_topic_0107283638_section51511928173817:

Viewing Logs
------------

You can view logs of Deployments, StatefulSets, DaemonSets, and jobs. This section uses a Deployment as an example to describe how to view logs.

#. Log in to the CCE console, go to an existing cluster, and choose **Workloads** in the navigation pane.

#. Click the **Deployments** tab and click the **View Log** of the target workload.

   On the displayed **View Log** window, you can view logs by time.

.. _cce_10_0007__en-us_topic_0107283638_section17604174417381:

Upgrading a Workload
--------------------

You quickly upgrade Deployments, StatefulSets, and DaemonSets on the CCE console.

This section uses a Deployment as an example to describe how to upgrade a workload.

Before replacing an image or image version, upload the new image to the SWR service.

#. Log in to the CCE console, go to an existing cluster, and choose **Workloads** in the navigation pane.
#. Click the **Deployments** tab and click **Upgrade** of the target workload.

   .. note::

      -  Workloads cannot be upgraded in batches.
      -  Before performing an in-place StatefulSet upgrade, you must manually delete old pods. Otherwise, the upgrade status is always displayed as **Upgrading**.

#. Upgrade the workload based on service requirements. The method for setting parameter is the same as that for creating a workload.
#. After the update is complete, click **Upgrade Workload**, manually confirm the YAML file, and submit the upgrade.

.. _cce_10_0007__en-us_topic_0107283638_section21669213390:

Editing a YAML file
-------------------

You can modify and download the YAML files of Deployments, StatefulSets, DaemonSets, and pods on the CCE console. YAML files of jobs and cron jobs can only be viewed, copied, and downloaded. This section uses a Deployment as an example to describe how to edit the YAML file.

#. Log in to the CCE console, go to an existing cluster, and choose **Workloads** in the navigation pane.
#. Click the **Deployments** tab and choose **More** > **Edit YAML** in the **Operation** column of the target workload. In the dialog box that is displayed, modify the YAML file.
#. Click **Edit** and then **OK** to save the changes.
#. (Optional) In the **Edit YAML** window, click **Download** to download the YAML file.

.. _cce_10_0007__en-us_topic_0107283638_section13324541124815:

Rolling Back a Workload (Available Only for Deployments)
--------------------------------------------------------

CCE records the release history of all Deployments. You can roll back a Deployment to a specified version.

#. Log in to the CCE console, go to an existing cluster, and choose **Workloads** in the navigation pane.
#. Click the **Deployments** tab, choose **More > Roll Back** in the **Operation** column of the target workload.
#. Switch to the **Change History** tab page, click **Roll Back to This Version** of the target version, manually confirm the YAML file, and click **OK**.

.. _cce_10_0007__section132451237607:

Redeploying a Workload
----------------------

After you redeploy a workload, all pods in the workload will be restarted. This section uses Deployments as an example to illustrate how to redeploy a workload.

#. Log in to the CCE console, go to an existing cluster, and choose **Workloads** in the navigation pane.
#. Click the **Deployments** tab and choose **More** > **Redeploy** in the **Operation** column of the target workload.
#. In the dialog box that is displayed, click **Yes** to redeploy the workload.

.. _cce_10_0007__en-us_topic_0107283638_section12087915401:

Disabling/Enabling Upgrade (Available Only for Deployments)
-----------------------------------------------------------

Only Deployments support this operation.

-  After the upgrade is disabled, the upgrade command can be delivered but will not be applied to the pods.

   If you are performing a rolling upgrade, the rolling upgrade stops after the disabling upgrade command is delivered. In this case, the new and old pods co-exist.

-  If a Deployment is being upgraded, it can be upgraded or rolled back. Its pods will inherit the latest updates of the Deployment. If they are inconsistent, the pods are upgraded automatically according to the latest information of the Deployment.

.. important::

   Deployments in the disable upgrade state cannot be rolled back.

#. Log in to the CCE console, go to an existing cluster, and choose **Workloads** in the navigation pane.
#. Click the **Deployments** tab and choose **More** > **Disable/Enable Upgrade** in the **Operation** column of the workload.
#. In the dialog box that is displayed, click **Yes**.

.. _cce_10_0007__en-us_topic_0107283638_section5931193015488:

Managing Labels
---------------

Labels are key-value pairs and can be attached to workloads. Workload labels are often used for affinity and anti-affinity scheduling. You can add labels to multiple workloads or a specified workload.

You can manage the labels of Deployments, StatefulSets, and DaemonSets based on service requirements. This section uses Deployments as an example to describe how to manage labels.

In the following figure, three labels (release, env, and role) are defined for workload APP 1, APP 2, and APP 3. The values of these labels vary with workload.

-  Label of APP 1: [release:alpha;env:development;role:frontend]
-  Label of APP 2: [release:beta;env:testing;role:frontend]
-  Label of APP 3: [release:alpha;env:production;role:backend]

If you set **key** to **role** and **value** to **frontend** when using workload scheduling or another function, APP 1 and APP 2 will be selected.


.. figure:: /_static/images/en-us_image_0000001517903028.png
   :alt: **Figure 1** Label example

   **Figure 1** Label example

#. Log in to the CCE console, go to an existing cluster, and choose **Workloads** in the navigation pane.
#. Click the **Deployments** tab and choose **More** > **Manage Label** in the **Operation** column of the target workload.
#. Click **Add**, enter a key and a value, and click **OK**.

   .. note::

      A key-value pair must contain 1 to 63 characters starting and ending with a letter or digit. Only letters, digits, hyphens (-), underscores (_), and periods (.) are allowed.

.. _cce_10_0007__en-us_topic_0107283638_section14423721191418:

Deleting a Workload/Job
-----------------------

You can delete a workload or job that is no longer needed. Deleted workloads or jobs cannot be recovered. Exercise caution when you perform this operation. This section uses a Deployment as an example to describe how to delete a workload.

#. Log in to the CCE console, go to an existing cluster, and choose **Workloads** in the navigation pane.

#. In the same row as the workload you will delete, choose **Operation** > **More** > **Delete**.

   Read the system prompts carefully. A workload cannot be recovered after it is deleted. Exercise caution when performing this operation.

#. Click **Yes**.

   .. note::

      -  If the node where the pod is located is unavailable or shut down and the workload cannot be deleted, you can forcibly delete the pod from the pod list on the workload details page.
      -  Ensure that the storage volumes to be deleted are not used by other workloads. If these volumes are imported or have snapshots, you can only unbind them.

.. _cce_10_0007__en-us_topic_0107283638_section1947616516301:

Viewing Events
--------------

This section uses Deployments as an example to illustrate how to view events of a workload. To view the event of a job or cron jon, click **View Event** in the **Operation** column of the target workload.

#. Log in to the CCE console, go to an existing cluster, and choose **Workloads** in the navigation pane.
#. On the **Deployments** tab page, click the target workload. In the **Pods** tab page, click the **View Events** to view the event name, event type, number of occurrences, Kubernetes event, first occurrence time, and last occurrence time.

   .. note::

      Event data will be retained for one hour and then automatically deleted.
