:original_name: cce_01_0007.html

.. _cce_01_0007:

Managing Workloads and Jobs
===========================

Scenario
--------

After a workload is created, you can scale, upgrade, monitor, roll back, or delete the workload, as well as edit its YAML file.

.. table:: **Table 1** Workload/Job management

   +--------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Operation                                                    | Description                                                                                                                                                                                                                                  |
   +==============================================================+==============================================================================================================================================================================================================================================+
   | :ref:`Logging <cce_01_0007__section51511928173817>`          | You can view logs of Deployments, StatefulSets, DaemonSets, and jobs.                                                                                                                                                                        |
   +--------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Upgrade <cce_01_0007__section17604174417381>`          | You can replace images or image tags to quickly upgrade Deployments, StatefulSets, and DaemonSets without interrupting services.                                                                                                             |
   +--------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Editing a YAML file <cce_01_0007__section21669213390>` | You can modify and download the YAML files of Deployments, StatefulSets, DaemonSets, and pods on the CCE console. YAML files of jobs and cron jobs can only be viewed, copied, and downloaded.                                               |
   +--------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Scaling <cce_01_0007__section11703514131711>`          | A workload can be automatically resized according to scaling policies, freeing you from the efforts to manually adjust resources for fluctuating service traffic. This saves you big on both resources and labors.                           |
   +--------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Monitoring <cce_01_0007__section15303324141816>`       | You can view the CPU and memory usage of Deployments, DaemonSets, and pods on the CCE console to determine the resource specifications you may need.                                                                                         |
   +--------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Rollback <cce_01_0007__section13324541124815>`         | Only Deployments can be rolled back.                                                                                                                                                                                                         |
   +--------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Pausing <cce_01_0007__section10207209154017>`          | Only Deployments can be paused.                                                                                                                                                                                                              |
   +--------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Resuming <cce_01_0007__section12087915401>`            | Only Deployments can be resumed.                                                                                                                                                                                                             |
   +--------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Labeling <cce_01_0007__section5931193015488>`          | Labels are key-value pairs and can be attached to workloads for affinity and anti-affinity scheduling.                                                                                                                                       |
   +--------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Deletion <cce_01_0007__section14423721191418>`         | You can delete a workload or job that is no longer needed. Deleted workloads or jobs cannot be recovered.                                                                                                                                    |
   +--------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Access settings <cce_01_0249>`                         | You can determine how your workloads can be accessed. For details, see :ref:`Overview <cce_01_0010>`.                                                                                                                                        |
   +--------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Scheduling policies <cce_01_0051>`                     | CCE supports custom and simple scheduling policies. **Custom scheduling policies** allow you to customize node affinity, workload affinity, and workload anti-affinity. **Simple scheduling policies** allow easy and convenient scheduling. |
   +--------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | :ref:`Event <cce_01_0007__section1947616516301>`             | CCE provides event names, event types, number of occurrences, Kubernetes events, first occurrence time, and last occurrence time by workload or pod.                                                                                         |
   +--------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_01_0007__section51511928173817:

Viewing Logs
------------

You can view logs of Deployments, StatefulSets, DaemonSets, and jobs. This section uses a Deployment as an example to describe how to view logs.

#. Log in to the CCE console. In the navigation pane, choose **Workloads** > **Deployments**.

#. In the same row as the workload you will view, click **Logs**.

   In the displayed **Logs** window, view the logs generated in the last 5 minutes, 30 minutes, or 1 hour.

.. _cce_01_0007__section17604174417381:

Upgrading a Workload
--------------------

You can replace images or image tags to quickly upgrade Deployments, StatefulSets, and DaemonSets without interrupting services.

This section uses a Deployment as an example to describe how to upgrade a workload.

Before replacing an image or image version, upload the new image to the SWR service.

#. Log in to the CCE console. In the navigation pane, choose **Workloads** > **Deployments**, and click **Upgrade** for the Deployment to be upgraded.

   .. note::

      -  Workloads cannot be upgraded in batches.
      -  Before performing an in-place StatefulSet upgrade, you must manually delete old pods. Otherwise, the upgrade status is always displayed as **Upgrading**.

#. Upgrade the Deployment.

   -  **Image Name**: To replace the Deployment image, click **Replace Image** and select a new image.
   -  **Image Version**: To replace the Deployment image version, select a new version from the **Image Version** drop-down list.
   -  **Container Name**: To change the container name, click |image1| next to **Container Name** and enter a new name.
   -  **Privileged Container**: After this function is enabled, the container can access all devices on the host.
   -  **Container Resources**: You can set the CPU, memory and GPU quotas.
   -  **Advanced Settings**:

      -  **Lifecycle**: Commands for starting and running containers can be set.

         -  **Start Command**: executed when the workload is started. For details, see :ref:`Setting Container Startup Commands <cce_01_0008>`.
         -  **Post-Start**: executed after the workload runs successfully. For more information, see :ref:`Setting Container Lifecycle Parameters <cce_01_0105>`.
         -  **Pre-Stop**: executed to delete logs or temporary files before the workload ends. For more information, see :ref:`Setting Container Lifecycle Parameters <cce_01_0105>`.

      -  **Health Check**: CCE provides two types of probes: liveness probe and readiness probe. They are used to determine whether containers and user services are running properly. For more information, see :ref:`Setting Health Check for a Container <cce_01_0112>`.

         -  **Liveness Probe**: used to restart the unhealthy container.
         -  **Readiness Probe**: used to change the container to the unready state when detecting that the container is unhealthy. In this way, service traffic will not be directed to the container.

      -  **Environment Variables**: Environment variables can be added to a container. In general, environment variables are used to set parameters.

         On the **Environment Variables** tab page, click **Add Environment Variable**. Currently, three types of environment variables are supported:

         -  **Added manually**: Set **Variable Name** and **Variable Value/Reference**.
         -  **Added from Secret**: Set **Variable Name** and select the desired secret name and data. A secret must be created in advance. For details, see :ref:`Creating a Secret <cce_01_0153>`.
         -  **Added from ConfigMap**: Set **Variable Name** and select the desired ConfigMap name and data. A ConfigMap must be created in advance. For details, see :ref:`Creating a ConfigMap <cce_01_0152>`.

            .. note::

               To edit an environment variable that has been set, click **Edit**. To delete an environment variable that has been set, click **Delete**.

      -  **Data Storage**: Data storage can be mounted to containers for persistent storage and high disk I/O. Local disks and cloud storage volumes are supported. For details, see :ref:`Storage (CSI) <cce_01_0042>`.

         .. note::

            You can add data storage volumes only when creating a StatefulSet.

      -  **Security Context**: Container permissions can be configured to protect CCE and other containers from being affected.

         Enter the user ID to set container permissions and prevent systems and other containers from being affected.

      -  **Log Policies**: Log collection policies and log directory can be configured to collect container logs for unified management and analysis. For details, see :ref:`Container Logs <cce_01_0018>`.

#. Click **Submit**.

.. _cce_01_0007__section21669213390:

Editing a YAML file
-------------------

You can modify and download the YAML files of Deployments, StatefulSets, DaemonSets, and pods on the CCE console. YAML files of jobs and cron jobs can only be viewed, copied, and downloaded. This section uses a Deployment as an example to describe how to edit the YAML file.

#. Log in to the CCE console. In the navigation pane, choose **Workloads** > **Deployments**.
#. In the same row as the workload you will edit, choose **Operation** > **More** > **Edit YAML**. In the **Edit YAML** window, edit the YAML file of the current workload.
#. Click **Edit** and then **OK** to save the changes.
#. (Optional) In the **Edit YAML** window, click **Download** to download the YAML file.

.. _cce_01_0007__section11703514131711:

Scaling a Workload
------------------

A workload can be automatically resized according to custom scaling policies, freeing you from the efforts to manually adjust the amount of resources for fluctuating service traffic. This saves you big on both resources and labors. This section uses a Deployment as an example to describe how to scale a workload.

#. Log in to the CCE console. In the navigation pane, choose **Workloads** > **Deployments**.

#. In the same row as the workload for which you will add a scaling policy, choose **Operation** > **More** > **Scaling**.

#. On the **Scaling** tab page, add or edit scaling policies. Scaling policies are classified as auto and manual scaling policies.

   For details, see :ref:`Scaling a Workload <cce_01_0057>`.

.. _cce_01_0007__section15303324141816:

Monitoring a Workload
---------------------

You can view the CPU and memory usage of Deployments, DaemonSets, and pods on the CCE console to determine the resource specifications you may need. This section uses a Deployment as an example to describe how to monitor a workload.

#. Log in to the CCE console. In the navigation pane, choose **Workloads** > **Deployments**.
#. Click the name of the Deployment to be monitored. On the displayed Deployment details page, click the **Monitoring** tab to view CPU usage and memory usage of the Deployment.
#. Click the **Pods** tab. Click |image2| next to a pod to be monitored and click **Monitoring**.
#. Check CPU usage and memory usage of the pod.

   -  CPU usage

      The horizontal axis indicates time while the vertical axis indicates the CPU usage. The green line indicates the CPU usage while the red line indicates the CPU usage limit.

      .. note::

         It takes some time to calculate CPU usage. Therefore, when CPU and memory usage are displayed for the first time, CPU usage is displayed about one minute later than memory usage.

         CPU and memory usage are displayed only for pods in the running state.

   -  Memory usage

      The horizontal axis indicates time while the vertical axis indicates the memory usage. The green line indicates the memory usage while the red line indicates the memory usage limit.

      .. note::

         Memory usage is displayed only for a running pod.

.. _cce_01_0007__section13324541124815:

Rolling Back a Workload (Available Only for Deployments)
--------------------------------------------------------

CCE records the release history of all Deployments. You can roll back a Deployment to a specified version.

#. Log in to the CCE console. In the navigation pane, choose **Workloads** > **Deployments**.
#. In the same row as the Deployment you will roll back, choose **Operation** > **More** > **Roll Back**.
#. In the **Roll Back to This Version** drop-down list, select the version to which you will roll back the Deployment. Then, click **OK**.

.. _cce_01_0007__section10207209154017:

Pausing a Workload (Available Only for Deployments)
---------------------------------------------------

You can pause Deployments. After a Deployment is paused, the upgrade command can be successfully issued but will not be applied to the pods.

If you are performing a rolling upgrade, the rolling upgrade stops after the pause command is issued. In this case, the new and old pods coexist.

#. Log in to the CCE console. In the navigation pane, choose **Workloads** > **Deployments**.
#. In the same row as the Deployment you will pause, choose **Operation** > **More** > **Pause**.
#. In the displayed **Pause Workload** dialog box, click **OK**.
#. Click **OK**.

   .. important::

      Deployments in the paused state cannot be rolled back.

.. _cce_01_0007__section12087915401:

Resuming a Workload (Available Only for Deployments)
----------------------------------------------------

You can resume paused Deployments. After a Deployment is resumed, it can be upgraded or rolled back. Its pods will inherit the latest updates of the Deployment. If they are inconsistent, the pods are upgraded automatically according to the latest information of the Deployment.

#. Log in to the CCE console. In the navigation pane, choose **Workloads** > **Deployments**.
#. In the same row as the Deployment you will resume, choose **Operation** > **More** > **Resume**.
#. In the displayed **Resume Workload** dialog box, click **OK**.

.. _cce_01_0007__section5931193015488:

Managing Labels
---------------

Labels are key-value pairs and can be attached to workloads. Workload labels are often used for affinity and anti-affinity scheduling. You can add labels to multiple workloads or a specified workload.

You can manage the labels of Deployments, StatefulSets, and DaemonSets based on service requirements. This section uses Deployments as an example to describe how to manage labels.

In the following figure, three labels (release, env, and role) are defined for workload APP 1, APP 2, and APP 3. The values of these labels vary with workload.

-  Label of APP 1: [release:alpha;env:development;role:frontend]
-  Label of APP 2: [release:beta;env:testing;role:frontend]
-  Label of APP 3: [release:alpha;env:production;role:backend]

If you set **key** to **role** and **value** to **frontend** when using workload scheduling or another function, APP 1 and APP 2 will be selected.


.. figure:: /_static/images/en-us_image_0165888686.png
   :alt: **Figure 1** Label example

   **Figure 1** Label example

#. Log in to the CCE console. In the navigation pane, choose **Workloads** > **Deployments**.
#. Click the name of the workload whose labels will be managed.
#. On the workload details page, click **Manage Label**. In the displayed dialog box, click **Add Label**. Enter the label key and value, and click **OK**.

   .. note::

      A key-value pair must contain 1 to 63 characters starting and ending with a letter or digit. Only letters, digits, hyphens (-), underscores (_), and periods (.) are allowed.

.. _cce_01_0007__section14423721191418:

Deleting a Workload/Job
-----------------------

You can delete a workload or job that is no longer needed. Deleted workloads or jobs cannot be recovered. Exercise caution when you perform this operation. This section uses a Deployment as an example to describe how to delete a workload.

#. Log in to the CCE console. In the navigation pane, choose **Workloads** > **Deployments**.

#. In the same row as the workload you will delete, choose **Operation** > **More** > **Delete**.

   Read the system prompts carefully. A workload cannot be recovered after it is deleted. Exercise caution when performing this operation.

#. Click **Yes**.

   .. note::

      -  If the node where the pod is located is unavailable or shut down and the workload cannot be deleted, you can forcibly delete the pod from the pod list on the workload details page.
      -  Ensure that the storage volumes to be deleted are not used by other workloads. If these volumes are imported or have snapshots, you can only unbind them.

.. _cce_01_0007__section1947616516301:

Events
------

On the workload details page, click the **Events** or **Pods** tab to view the events, event types, number of occurrences, Kubernetes events, first occurrence time, and last occurrence time.

.. note::

   Event data will be retained for one hour and then automatically deleted.

.. |image1| image:: /_static/images/en-us_image_0195434915.png
.. |image2| image:: /_static/images/en-us_image_0121749065.png
