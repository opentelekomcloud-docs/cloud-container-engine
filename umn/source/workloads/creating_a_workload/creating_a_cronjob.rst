:original_name: cce_10_0151.html

.. _cce_10_0151:

Creating a CronJob
==================

Scenario
--------

A CronJob runs on a repeating schedule. You can perform time synchronization for all active nodes at a fixed time point.

A CronJob runs periodically at the specified time. It is similar with Linux crontab. A CronJob has the following characteristics:

-  Runs only once at the specified time.
-  Runs periodically at the specified time.

The typical usage of a CronJob is as follows:

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
   -  **Workload Name**: Enter the name of the workload. Enter 1 to 63 characters starting with a lowercase letter and ending with a lowercase letter or digit. Only lowercase letters, digits, and hyphens (-) are allowed.
   -  **Namespace**: Select the namespace of the workload. The default value is **default**. You can also click **Create Namespace** to create one. For details, see :ref:`Creating a Namespace <cce_10_0278>`.
   -  **Container Runtime**: A CCE standard cluster uses a common runtime by default, whereas a CCE Turbo cluster supports both common and secure runtimes. For details about the differences, see :ref:`Secure Runtime and Common Runtime <cce_10_0463>`.

   **Container Settings**

   -  Container Information

      Multiple containers can be configured in a pod. You can click **Add Container** on the right to configure multiple containers for the pod.

      -  **Basic Info**: Configure basic information about the container.

         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                         |
         +===================================+=====================================================================================================================================================================================================================================================================================================================================================================================================================================+
         | Container Name                    | Name the container.                                                                                                                                                                                                                                                                                                                                                                                                                 |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Pull Policy                       | Image update or pull policy. If you select **Always**, the image is pulled from the image repository each time. If you do not select **Always**, the existing image of the node is preferentially used. If the image does not exist, the image is pulled from the image repository.                                                                                                                                                 |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Image Name                        | Click **Select Image** and select the image used by the container.                                                                                                                                                                                                                                                                                                                                                                  |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | To use a third-party image, directly enter image path. Ensure that the :ref:`image access credential <cce_10_0047__li1487514116369>` can be used to access the image repository. For details, see :ref:`Using Third-Party Images <cce_10_0009>`.                                                                                                                                                                                    |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Image Tag                         | Select the image tag to be deployed.                                                                                                                                                                                                                                                                                                                                                                                                |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | CPU Quota                         | -  **Request**: minimum number of CPU cores required by a container. The default value is 0.25 cores.                                                                                                                                                                                                                                                                                                                               |
         |                                   | -  **Limit**: maximum number of CPU cores that can be used by a container. This prevents containers from using excessive resources.                                                                                                                                                                                                                                                                                                 |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | If **Request** and **Limit** are not specified, the quota is not limited. For more information and suggestions about **Request** and **Limit**, see :ref:`Configuring Container Specifications <cce_10_0163>`.                                                                                                                                                                                                                      |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Memory Quota                      | -  **Request**: minimum amount of memory required by a container. The default value is 512 MiB.                                                                                                                                                                                                                                                                                                                                     |
         |                                   | -  **Limit**: maximum amount of memory available for a container. When memory usage exceeds the specified memory limit, the container will be terminated.                                                                                                                                                                                                                                                                           |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | If **Request** and **Limit** are not specified, the quota is not limited. For more information and suggestions about **Request** and **Limit**, see :ref:`Configuring Container Specifications <cce_10_0163>`.                                                                                                                                                                                                                      |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | (Optional) GPU Quota              | Configurable only when the cluster contains GPU nodes and the :ref:`CCE AI Suite (NVIDIA GPU) <cce_10_0141>` add-on has been installed.                                                                                                                                                                                                                                                                                             |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | -  **Do not use**: No GPU will be used.                                                                                                                                                                                                                                                                                                                                                                                             |
         |                                   | -  **GPU card**: The GPU is dedicated for the container.                                                                                                                                                                                                                                                                                                                                                                            |
         |                                   | -  **GPU Virtualization**: percentage of GPU resources used by the container. For example, if this parameter is set to **10%**, the container will use 10% of GPU resources.                                                                                                                                                                                                                                                        |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | For details about how to use GPUs in the cluster, see :ref:`Default GPU Scheduling in Kubernetes <cce_10_0345>`.                                                                                                                                                                                                                                                                                                                    |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | (Optional) Privileged Container   | Programs in a privileged container have certain privileges.                                                                                                                                                                                                                                                                                                                                                                         |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | If **Privileged Container** is enabled, the container is assigned privileges. For example, privileged containers can manipulate network devices on the host machine and modify kernel parameters.                                                                                                                                                                                                                                   |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | (Optional) Init Container         | Whether to use the container as an init container. An init container does not support health check.                                                                                                                                                                                                                                                                                                                                 |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | An init container is a special container that runs before other app containers in a pod are started. Each pod can contain multiple containers. In addition, a pod can contain one or more init containers. Application containers in a pod are started and run only after the running of all init containers completes. For details, see `Init Containers <https://kubernetes.io/docs/concepts/workloads/pods/init-containers/>`__. |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | (Optional) Run Option             | Add run options for the container. For details, see `Pod <https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/pod-v1/>`__. CCE supports the following run options:                                                                                                                                                                                                                                               |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | -  **stdin**: allows containers to receive input from external sources, such as terminals or other input streams.                                                                                                                                                                                                                                                                                                                   |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | -  **tty**: allocates a pseudo terminal to containers, allowing you to send commands to them as if you were using a local terminal.                                                                                                                                                                                                                                                                                                 |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   |    In most cases, tty is enabled along with stdin, indicating that the terminal (tty) is associated with the standard input (stdin) of the container. This allows for interactive operations, similar to the **kubectl exec -i -t** command. The difference is that this parameter has been configured when the pod is launched.                                                                                                    |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

      -  (Optional) **Lifecycle**: Configure operations to be performed in a specific phase of the container lifecycle, such as Startup Command, Post-Start, and Pre-Stop. For details, see :ref:`Configuring Container Lifecycle Parameters <cce_10_0105>`.
      -  (Optional) **Environment Variables**: Configure variables for the container running environment using key-value pairs. These variables transfer external information to containers running in pods and can be flexibly modified after application deployment. For details, see :ref:`Configuring Environment Variables <cce_10_0113>`.

   -  **Image Access Credential**: Select the credential used for accessing the image repository. The default value is **default-secret**. You can use default-secret to access images in SWR Shared Edition. For details about **default-secret**, see :ref:`default-secret <cce_10_0388__section11760122012591>`.

   -  (Optional) **GPU**: **All** is selected by default. The workload instance will be scheduled to the node of the specified GPU type.

   **Schedule**

   -  **Concurrency Policy**: The following three modes are supported:

      -  **Forbid**: A new job cannot be created before the previous job is completed.
      -  **Allow**: The CronJob allows concurrently running jobs, which preempt cluster resources.
      -  **Replace**: A new job replaces the previous job when it is time to create a job but the previous job is not completed.

   -  **Policy Settings**: specifies when a new CronJob is executed. Policy settings in YAML are implemented using cron expressions.

      -  A CronJob is executed at a fixed interval. The unit can be minute, hour, day, or month. For example, if a CronJob is executed every 30 minutes, its cron expression is **\*/30 \* \* \* \***. The job will be executed at 30-minute intervals, starting from the top of the hour, for example, **00:00:00**, **00:30:00**, **01:00:00**, and **...**.
      -  The CronJob is executed at a fixed time (by month). For example, if a CronJob is executed at 00:00 on the first day of each month, its cron expression is **0 0 1 \*/1 \***. The job will be executed at **\****-01-01 00:00:00**, **\****-02-01 00:00:00**, and **...**.
      -  The CronJob is executed by week. For example, if a CronJob is executed at 00:00 every Monday, its cron expression is **0 0 \* \* 1**. The job will be executed at **\****-**-01 00:00:00 on Monday**, **\****-**-08 00:00:00 on Monday**, and **...**.
      -  **Custom Cron Expression**: For details about how to use cron expressions, see `CronJob <https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/#cron-schedule-syntax>`__.

      .. note::

         -  If a CronJob is executed at a fixed time (by month) and the number of days in a month does not exist, the CronJob will not be executed in this month. For example, the execution will skip February if the date is set to 30.

         -  Due to the definition of cron, the fixed period is not a strict period. The time unit range is divided from 0 by period. For example, if the unit is minute, the value ranges from 0 to 59. If the value cannot be exactly divided, the last period is reset. Therefore, an accurate period can be represented only when the period can be evenly divided.

            Take a CronJob that runs hourly as an example. If an interval that can divide 24 hours exactly, such as **/2, /3, /4, /6, /8, and /12**, the period can be accurately represented. However, if a different period is used, the last period will reset at the beginning of each new day. For example, if the cron expression is **\* \*/12 \* \* \***, the task will run at **00:00:00** and **12:00:00** every day. Similarly, if the cron expression is **\* \*/13 \* \* \***, the task will run at **00:00:00** and **13:00:00** every day. Note that the last period will reset at 00:00:00 on the following day, even if it has not reached 13 hours.

   -  **Time Zone**: You can specify a time zone. If this parameter is not specified, the time zone of the master node will be used by default.
   -  **Job Records**: You can set the number of jobs that are successfully executed or fail to be executed. Setting a limit to **0** corresponds to keeping none of the jobs after they finish.

   **(Optional) Advanced Settings**

   -  **Labels and Annotations**: Add labels or annotations for pods using key-value pairs. After entering the key and value, click **Confirm**. For details about how to use and configure labels and annotations, see :ref:`Configuring Labels and Annotations <cce_10_0386>`.

   -  **Network Configuration**

      -  Pod ingress/egress bandwidth limitation: You can set ingress/egress bandwidth limitation for pods. For details, see :ref:`Configuring QoS for a Pod <cce_10_0382>`.
      -  Whether to enable a specified container network configuration: available only for clusters that support this function. After you enable a specified container network configuration, the workload will be created using the container subnet and security group in the configuration. For details, see :ref:`Binding a Subnet and Security Group to a Namespace or Workload Using a Container Network Configuration <cce_10_0196>`.
      -  Specify the container network configuration name: Only the custom container network configuration whose associated resource type is workload can be selected.
      -  IPv6 shared bandwidth: available only for clusters that support this function. After this function is enabled, you can configure a shared bandwidth for a pod with IPv6 dual-stack ENIs. For details, see :ref:`Configuring Shared Bandwidth for a Pod with IPv6 Dual-Stack ENIs <cce_10_0604>`.

#. Click **Create Workload** in the lower right corner and check whether the workload status changes to **Started**.

Using kubectl
-------------

A CronJob has the following configuration parameters:

-  **.spec.schedule**: takes a `Cron <https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/#cron-schedule-syntax>`__ format string, for example, **0 \* \* \* \*** or **@hourly**, as schedule time of jobs to be created and executed.
-  **.spec.jobTemplate**: specifies jobs to be run, and has the same schema as when you are :ref:`Creating a Job Using kubectl <cce_10_0150__section450152719412>`.
-  **.spec.startingDeadlineSeconds**: specifies the deadline for starting a job.
-  **.spec.concurrencyPolicy**: specifies how to treat concurrent executions of a job created by the CronJob. The following options are supported:

   -  **Allow** (default value): allows concurrently running jobs.
   -  **Forbid**: forbids concurrent runs, skipping next run if previous has not finished yet.
   -  **Replace**: cancels the currently running job and replaces it with a new one.

The following is an example CronJob, which is saved in the **cronjob.yaml** file.

.. note::

   In clusters of v1.21 or later, CronJob apiVersion is **batch/v1**.

   In clusters earlier than v1.21, CronJob apiVersion is **batch/v1beta1**.

.. code-block::

   apiVersion: batch/v1
   kind: CronJob
   metadata:
     name: hello
   spec:
     schedule: "*/1 * * * *"  # The task is executed every minute.
     jobTemplate:
       spec:
         template:
           spec:
             containers:
             - name: hello
               image: busybox
               command:
               - /bin/sh
               - -c
               - date; echo Hello from the Kubernetes cluster
             restartPolicy: OnFailure
             imagePullSecrets:
               - name: default-secret

**Run the job.**

#. Create a CronJob.

   .. code-block::

      kubectl create -f cronjob.yaml

   Information similar to the following is displayed:

   .. code-block::

      cronjob.batch/hello created

#. Check the running status of the CronJob:

   .. code-block::

      kubectl get cronjob

   The command output is as follows:

   .. code-block::

      NAME    SCHEDULE      TIMEZONE   SUSPEND   ACTIVE   LAST SCHEDULE   AGE
      hello   */1 * * * *   <none>     False     0        59s             2m36s

#. Check the job that is started at a scheduled time.

   .. code-block::

      kubectl get jobs

   The command output is as follows:

   .. code-block::

      NAME               COMPLETIONS   DURATION   AGE
      hello-1597387980   1/1           27s        45s

#. Check the pods started by the job.

   .. code-block::

      kubectl get pod

   The command output is as follows:

   .. code-block::

      NAME                           READY     STATUS      RESTARTS   AGE
      hello-1597387980-tjv8f         0/1       Completed   0          114s

#. Check the pod logs. It is expected that "Hello from the Kubernetes cluster" is displayed in the logs.

   .. code-block::

      kubectl logs hello-1597387980-tjv8f

   The command output is as follows:

   .. code-block::

      Fri Aug 14 06:56:31 UTC 2020
      Hello from the Kubernetes cluster

#. Delete the cron job. If a cron job is deleted, the related jobs and pods are deleted accordingly.

   .. code-block::

      kubectl delete cronjob hello

   The command output is as follows:

   .. code-block::

      cronjob.batch "hello" deleted

Related Operations
------------------

After a CronJob is created, you can perform operations listed in :ref:`Table 1 <cce_10_0151__t6d520710097a4ee098eae42bcb508608>`.

.. _cce_10_0151__t6d520710097a4ee098eae42bcb508608:

.. table:: **Table 1** Other operations

   +-----------------------------------+---------------------------------------------------------------------------------------------------+
   | Operation                         | Description                                                                                       |
   +===================================+===================================================================================================+
   | Editing a YAML file               | Click **More** > **Edit YAML** next to the CronJob name to edit the YAML file of the current job. |
   +-----------------------------------+---------------------------------------------------------------------------------------------------+
   | Stopping a CronJob                | #. Select the job to be stopped and click **Stop** in the **Operation** column.                   |
   |                                   | #. Click **Yes**.                                                                                 |
   +-----------------------------------+---------------------------------------------------------------------------------------------------+
   | Deleting a CronJob                | #. Select the CronJob to be deleted and click **More** > **Delete** in the **Operation** column.  |
   |                                   |                                                                                                   |
   |                                   | #. Click **Yes**.                                                                                 |
   |                                   |                                                                                                   |
   |                                   |    Deleted jobs cannot be restored. Exercise caution when deleting a job.                         |
   +-----------------------------------+---------------------------------------------------------------------------------------------------+
