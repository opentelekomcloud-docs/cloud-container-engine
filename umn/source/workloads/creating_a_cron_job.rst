:original_name: cce_01_0151.html

.. _cce_01_0151:

Creating a Cron Job
===================

Scenario
--------

A cron job runs on a repeating schedule. You can perform time synchronization for all active nodes at a fixed time point.

A cron job runs periodically at the specified time. It is similar with Linux crontab. A cron job has the following characteristics:

-  Runs only once at the specified time.
-  Runs periodically at the specified time.

The typical usage of a cron job is as follows:

-  Schedules jobs at the specified time.
-  Creates jobs to run periodically, for example, database backup and email sending.

Prerequisites
-------------

Resources have been created. For details, see :ref:`Creating a Node <cce_01_0033>`. If clusters and nodes are available, you need not create them again.

Procedure
---------

#. (Optional) If you use a private container image to create your cron job, upload the container image to the image repository.

#. Log in to the CCE console. In the navigation pane, choose **Workloads** > **Cron Jobs**. Then, click **Create Cron Job**.

#. Configure the basic cron job information listed in :ref:`Table 1 <cce_01_0151__tfadd3a11520b424d9063fe347c9c8c46>`. The parameters marked with an asterisk (*) are mandatory.

   .. _cce_01_0151__tfadd3a11520b424d9063fe347c9c8c46:

   .. table:: **Table 1** Basic cron job information

      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                               |
      +===================================+===========================================================================================================================================================+
      | \* Job Name                       | Name of a new cron job. The name must be unique.                                                                                                          |
      |                                   |                                                                                                                                                           |
      |                                   | Enter 4 to 52 characters starting with a lowercase letter and ending with a letter or digit. Only lowercase letters, digits, and hyphens (-) are allowed. |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Cluster                        | Cluster to which a new cron job belongs.                                                                                                                  |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Namespace                      | Namespace to which a cron job belongs. If you do not specify this parameter, the value **default** is used by default.                                    |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Description                       | Description of a cron job.                                                                                                                                |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Next: Configure Timing Rule**.

#. Set the timing rule.

   .. table:: **Table 2** Timing rule parameters

      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                               |
      +===================================+===========================================================================================================================================================================+
      | \* Concurrency Policy             | The following policies are supported:                                                                                                                                     |
      |                                   |                                                                                                                                                                           |
      |                                   | -  Forbid: A new job cannot be created before the previous job is complete.                                                                                               |
      |                                   | -  Allow: The cron job allows concurrently running jobs, which preempt cluster resources.                                                                                 |
      |                                   | -  Replace: A new job replaces the previous job when it is time to create the job but the previous job is not complete.                                                   |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Schedule                       | Time when a new cron job is executed.                                                                                                                                     |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Job Records                       | You can set the number of jobs that are successfully executed or fail to be executed. Setting a limit to **0** corresponds to keeping none of the jobs after they finish. |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Next: Add Container** to add a container.

   a. Click **Select Container Image** to select the image to be deployed.

      -  **My Images**: displays all image repositories you created.
      -  **Third-Party Images**: Create a job using an image from any third-party image repository. When you create a job using a third-party image, ensure that the node where the job is running can access public networks. For details about how to use a third-party image, see :ref:`Using a Third-Party Image <cce_01_0009>`.

         -  If your image repository does not require authentication, set **Secret Authentication** to **No**, enter an image address in **Image Address**, and then click **OK**.
         -  If your image repository must be authenticated (account and password), you need to create a secret and then use a third-party image. For details, see :ref:`Using a Third-Party Image <cce_01_0009>`.

      -  **Shared Images**: The images shared by other tenants using the SWR service are displayed here. You can create workloads based on the shared images.

   b. Set image parameters.

      .. table:: **Table 3** Image parameters

         +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                                                                                                                                  |
         +===================================+==============================================================================================================================================================================================================================================================================================================+
         | Image                             | Name of the image. You can click **Change Image** to update it.                                                                                                                                                                                                                                              |
         +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | \*Image Version                   | Select the image tag to be deployed.                                                                                                                                                                                                                                                                         |
         +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | \*Container Name                  | Name of the container. You can modify it.                                                                                                                                                                                                                                                                    |
         +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Container Resources               | **CPU**                                                                                                                                                                                                                                                                                                      |
         |                                   |                                                                                                                                                                                                                                                                                                              |
         |                                   | -  **Request**: minimum number of CPU cores required by a container. The default value is 0.25 cores.                                                                                                                                                                                                        |
         |                                   | -  **Limit**: maximum number of CPU cores available for a container. Do not leave **Limit** unspecified. Otherwise, intensive use of container resources will occur and your workload may exhibit unexpected behavior.                                                                                       |
         |                                   |                                                                                                                                                                                                                                                                                                              |
         |                                   | **Memory**                                                                                                                                                                                                                                                                                                   |
         |                                   |                                                                                                                                                                                                                                                                                                              |
         |                                   | -  **Request**: minimum amount of memory required by a container. The default value is 0.5 GiB.                                                                                                                                                                                                              |
         |                                   | -  **Limit**: maximum amount of memory available for a container. When memory usage exceeds the specified memory limit, the container will be terminated.                                                                                                                                                    |
         |                                   |                                                                                                                                                                                                                                                                                                              |
         |                                   | For more information about **Request** and **Limit**, see :ref:`Setting Container Specifications <cce_01_0163>`.                                                                                                                                                                                             |
         |                                   |                                                                                                                                                                                                                                                                                                              |
         |                                   | **GPU**: configurable only when the cluster contains GPU nodes.                                                                                                                                                                                                                                              |
         |                                   |                                                                                                                                                                                                                                                                                                              |
         |                                   | It indicates the percentage of GPU resources reserved for a container. Select **Use** and set the percentage. For example, if this parameter is set to 10%, the container is allowed to use 10% of GPU resources. If you do not select **Use** or set this parameter to **0**, no GPU resources can be used. |
         |                                   |                                                                                                                                                                                                                                                                                                              |
         |                                   | **GPU/Graphics Card**: The workload's pods will be scheduled to the node with the specified GPU.                                                                                                                                                                                                             |
         |                                   |                                                                                                                                                                                                                                                                                                              |
         |                                   | If **Any GPU type** is selected, the container uses a random GPU in the node. If you select a specific GPU, the container uses that GPU accordingly.                                                                                                                                                         |
         +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   c. (Optional) Configure advanced settings.

      .. table:: **Table 4** Advanced settings

         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                                                                                                       |
         +===================================+===================================================================================================================================================================================================================================================================================+
         | Lifecycle                         | Actions defined in the lifecycle script definition are taken for the lifecycle events of container tasks.                                                                                                                                                                         |
         |                                   |                                                                                                                                                                                                                                                                                   |
         |                                   | -  **Start Command**: You can set the command to be executed immediately after the container is started. For details, see :ref:`Configuring a Container <cce_01_0130>`.                                                                                                           |
         |                                   | -  **Post-Start**: The command is triggered after a job starts. For details, see :ref:`Setting Container Lifecycle Parameters <cce_01_0105>`.                                                                                                                                     |
         |                                   | -  **Pre-Stop**: The command is triggered before a job is stopped. For details, see :ref:`Setting Container Lifecycle Parameters <cce_01_0105>`.                                                                                                                                  |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Environment Variables             | Environment variables can be added to a container. In general, environment variables are used to set parameters. On the **Environment Variables** tab page, click **Add Environment Variable**. Currently, environment variables can be added using any of the following methods: |
         |                                   |                                                                                                                                                                                                                                                                                   |
         |                                   | -  **Added manually**: Set **Variable Name** and **Variable Value/Reference**.                                                                                                                                                                                                    |
         |                                   | -  **Added from Secret**: Set **Variable Name** and select the desired secret name and data. A secret must be created in advance. For details, see :ref:`Creating a Secret <cce_01_0153>`.                                                                                        |
         |                                   | -  **Added from ConfigMap**: Set **Variable Name** and select the desired ConfigMap name and data. A ConfigMap must be created in advance. For details, see :ref:`Creating a ConfigMap <cce_01_0152>`.                                                                            |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   d. (Optional) One job pod contains one or more related containers. If your cron job contains multiple containers, click **Add Container** to add containers.

#. Click **Create**.

   If the status is **Started**, the cron job has been created successfully.

.. _cce_01_0151__section13519162224919:

Using kubectl
-------------

A cron job has the following configuration parameters:

-  **.spec.schedule**: takes a `Cron <https://en.wikipedia.org/wiki/Cron>`__ format string, for example, **0 \* \* \* \*** or **@hourly**, as schedule time of jobs to be created and executed.
-  **.spec.jobTemplate**: specifies jobs to be run, and has the same schema as when you are :ref:`Creating a Job Using kubectl <cce_01_0150__section450152719412>`.
-  **.spec.startingDeadlineSeconds**: specifies the deadline for starting a job.
-  **.spec.concurrencyPolicy**: specifies how to treat concurrent executions of a job created by the Cron job. The following options are supported:

   -  **Allow** (default value): allows concurrently running jobs.
   -  **Forbid**: forbids concurrent runs, skipping next run if previous has not finished yet.
   -  **Replace**: cancels the currently running job and replaces it with a new one.

The following is an example cron job, which is saved in the **cronjob.yaml** file.

.. code-block::

   apiVersion: batch/v1beta1
   kind: CronJob
   metadata:
     name: hello
   spec:
     schedule: "*/1 * * * *"
     jobTemplate:
       spec:
         template:
           spec:
             containers:
             - name: hello
               image: busybox
               args:
               - /bin/sh
               - -c
               - date; echo Hello from the Kubernetes cluster
             restartPolicy: OnFailure

**Run the job.**

#. Create a cron job.

   **kubectl create -f cronjob.yaml**

   Information similar to the following is displayed:

   .. code-block::

      cronjob.batch/hello created

#. Query the running status of the cron job:

   **kubectl get cronjob**

   .. code-block::

      NAME      SCHEDULE      SUSPEND   ACTIVE    LAST SCHEDULE   AGE
      hello     */1 * * * *   False     0         <none>          9s

   **kubectl get jobs**

   .. code-block::

      NAME               COMPLETIONS   DURATION   AGE
      hello-1597387980   1/1           27s        45s

   **kubectl get pod**

   .. code-block::

      NAME                           READY     STATUS      RESTARTS   AGE
      hello-1597387980-tjv8f         0/1       Completed   0          114s
      hello-1597388040-lckg9         0/1       Completed   0          39s

   **kubectl logs** **hello-1597387980-tjv8f**

   .. code-block::

      Fri Aug 14 06:56:31 UTC 2020
      Hello from the Kubernetes cluster

   **kubectl delete cronjob hello**

   .. code-block::

      cronjob.batch "hello" deleted

   .. important::

      When a cron job is deleted, the related jobs and pods are deleted too.

Related Operations
------------------

After a cron job is created, you can perform operations listed in :ref:`Table 5 <cce_01_0151__t6d520710097a4ee098eae42bcb508608>`.

.. _cce_01_0151__t6d520710097a4ee098eae42bcb508608:

.. table:: **Table 5** Other operations

   +-----------------------------------+----------------------------------------------------------------------------------------------------+
   | Operation                         | Description                                                                                        |
   +===================================+====================================================================================================+
   | Editing a YAML file               | Click **More** > **View YAML** next to the cron job name to view the YAML file of the current job. |
   +-----------------------------------+----------------------------------------------------------------------------------------------------+
   | Stopping a cron job               | #. Select the job to be stopped and click **Stop** in the **Operation** column.                    |
   |                                   | #. Click **OK**.                                                                                   |
   +-----------------------------------+----------------------------------------------------------------------------------------------------+
   | Deleting a cron job               | #. Select the cron job to be deleted and click **More** > **Delete** in the **Operation** column.  |
   |                                   |                                                                                                    |
   |                                   | #. Click **OK**.                                                                                   |
   |                                   |                                                                                                    |
   |                                   |    Deleted jobs cannot be restored. Therefore, exercise caution when deleting a job.               |
   +-----------------------------------+----------------------------------------------------------------------------------------------------+
