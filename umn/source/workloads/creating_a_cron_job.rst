:original_name: cce_10_0151.html

.. _cce_10_0151:

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

Resources have been created. For details, see :ref:`Creating a Node <cce_10_0363>`.

Using the CCE Console
---------------------

#. Log in to the CCE console.

#. Click the cluster name to go to the cluster console, choose **Workloads** in the navigation pane, and click the **Create Workload** in the upper right corner.

#. Set basic information about the workload.

   **Basic Info**

   -  **Workload Type**: Select **Cron Job**. For details about workload types, see :ref:`Overview <cce_10_0006>`.
   -  **Workload Name**: Enter the name of the workload.
   -  **Namespace**: Select the namespace of the workload. The default value is **default**. You can also click **Create Namespace** to create one. For details, see :ref:`Creating a Namespace <cce_10_0278>`.
   -  **Container Runtime**: A CCE cluster uses runC by default, whereas a CCE Turbo cluster supports both runC and Kata. For details about the differences between runC and Kata, see :ref:`Kata Containers and Common Containers <cce_10_0463>`.

   **Container Settings**

   -  Container Information

      Multiple containers can be configured in a pod. You can click **Add Container** on the right to configure multiple containers for the pod.

      -  **Basic Info**: See :ref:`Setting Basic Container Information <cce_10_0396>`.
      -  **Lifecycle**: See :ref:`Setting Container Lifecycle Parameters <cce_10_0105>`.
      -  **Environment Variables**: See :ref:`Setting an Environment Variable <cce_10_0113>`.

   -  **Image Access Credential**: Select the credential used for accessing the image repository. The default value is **default-secret**. You can use default-secret to access images in SWR. For details about **default-secret**, see :ref:`default-secret <cce_10_0388__section11760122012591>`.

   -  **GPU graphics card**: **All** is selected by default. The workload instance will be scheduled to the node with the specified GPU graphics card type.

   **Schedule**

   -  **Concurrency Policy**: The following three modes are supported:

      -  **Forbid**: A new job cannot be created before the previous job is completed.
      -  **Allow**: The cron job allows concurrently running jobs, which preempt cluster resources.
      -  **Replace**: A new job replaces the previous job when it is time to create a job but the previous job is not completed.

   -  **Policy Settings**: specifies when a new cron job is executed. Policy settings in YAML are implemented using cron expressions.

      -  A cron job is executed at a fixed interval. The unit can be minute, hour, day, or month. For example, if a cron job is executed every 30 minutes, the cron expression is **\*/30 \* \* \* \***, the execution time starts from 0 in the unit range, for example, **00:00:00**, **00:30:00**, **01:00:00**, and **...**.
      -  The cron job is executed at a fixed time (by month). For example, if a cron job is executed at 00:00 on the first day of each month, the cron expression is **0 0 1 \*/1 \***, and the execution time is **\****-01-01 00:00:00**, **\****-02-01 00:00:00**, and **...**.
      -  The cron job is executed at a fixed time (by week). For example, if a cron job is executed at 00:00 every Monday, the cron expression is **0 0 \* \* 1**, and the execution time is **\****-**-01 00:00:00 on Monday**, **\****-**-08 00:00:00 on Monday**, and **...**.
      -  For details about how to use cron expressions, see `cron <https://en.wikipedia.org/wiki/Cron>`__.

      .. note::

         -  If a cron job is executed at a fixed time (by month) and the number of days in a month does not exist, the cron job will not be executed in this month. For example, if the number of days is set to 30 but February does not have the 30th day, the cron job skips this month and continues on March 30.

         -  Due to the definition of the cron expression, the fixed period is not a strict period. The time unit range is divided from 0 by period. For example, if the unit is minute, the value ranges from 0 to 59. If the value cannot be exactly divided, the last period is reset. Therefore, an accurate period can be represented only when the period can evenly divide its time unit range.

            For example, the unit of the period is hour. Because **/2, /3, /4, /6, /8, and /12** can be divided by 24, the accurate period can be represented. If another period is used, the last period will be reset at the beginning of a new day. For example, if the cron expression is **\* \*/12 \* \* \***, the execution time is **00:00:00** and **12:00:00** every day. If the cron expression is **\* \*/13 \* \* \***, the execution time is **00:00:00** and **13:00:00** every day. At 00:00 on the next day, the execution time is updated even if the period does not reach 13 hours.

   -  **Job Records**: You can set the number of jobs that are successfully executed or fail to be executed. Setting a limit to **0** corresponds to keeping none of the jobs after they finish.

   **Advanced Settings**

   -  **Labels and Annotations**: See :ref:`Pod Labels and Annotations <cce_10_0386>`.

#. Click **Create Workload** in the lower right corner.

Using kubectl
-------------

A cron job has the following configuration parameters:

-  **.spec.schedule**: takes a `Cron <https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/#cron-schedule-syntax>`__ format string, for example, **0 \* \* \* \*** or **@hourly**, as schedule time of jobs to be created and executed.
-  **.spec.jobTemplate**: specifies jobs to be run, and has the same schema as when you are :ref:`Creating a Job Using kubectl <cce_10_0150__section450152719412>`.
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

After a cron job is created, you can perform operations listed in :ref:`Table 1 <cce_10_0151__t6d520710097a4ee098eae42bcb508608>`.

.. _cce_10_0151__t6d520710097a4ee098eae42bcb508608:

.. table:: **Table 1** Other operations

   +-----------------------------------+----------------------------------------------------------------------------------------------------+
   | Operation                         | Description                                                                                        |
   +===================================+====================================================================================================+
   | Editing a YAML file               | Click **More** > **Edit YAML** next to the cron job name to edit the YAML file of the current job. |
   +-----------------------------------+----------------------------------------------------------------------------------------------------+
   | Stopping a cron job               | #. Select the job to be stopped and click **Stop** in the **Operation** column.                    |
   |                                   | #. Click **Yes**.                                                                                  |
   +-----------------------------------+----------------------------------------------------------------------------------------------------+
   | Deleting a cron job               | #. Select the cron job to be deleted and click **More** > **Delete** in the **Operation** column.  |
   |                                   |                                                                                                    |
   |                                   | #. Click **Yes**.                                                                                  |
   |                                   |                                                                                                    |
   |                                   |    Deleted jobs cannot be restored. Therefore, exercise caution when deleting a job.               |
   +-----------------------------------+----------------------------------------------------------------------------------------------------+
