:original_name: cce_10_0151.html

.. _cce_10_0151:

Creating a CronJob
==================

A `CronJob <https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/>`__ is a Kubernetes workload designed to run periodic tasks, similar to crontab in Linux. CronJobs follow a `Cron <https://man7.org/linux/man-pages/man8/cron.8.html>`__ format. They periodically execute jobs at predefined schedules.

CronJobs help automate and manage tasks, ensuring that tasks are executed on time and reducing manual management workloads. They are ideal for various scenarios, including:

-  **Periodic backups**: There is a need to periodically execute backup tasks, such as database backups and file system backups.
-  **Data synchronization**: Data is periodically synchronized from the primary node to a secondary node or cloud storage.
-  **Log clearing**: There is a need to periodically clear old log files to free up storage space.
-  **Periodic report**: There is a need to periodically generate and send reports, such as system status reports and performance reports.
-  **Maintenance tasks**: There is a need to execute regular system maintenance tasks, such as cache clearing and component updates.
-  **Scheduled tasks**: There is a need to execute specific tasks at a specific time point or within a period, such as service restarts and health checks.

Prerequisites
-------------

-  A cluster is available. For details about how to create a cluster, see :ref:`Creating a CCE Standard/Turbo Cluster <cce_10_0028>`.
-  There are some available nodes in the cluster. If no node is available, create one by referring to :ref:`Creating a Node <cce_10_0363>`.

Using the CCE Console
---------------------

#. Log in to the CCE console.
#. Click the cluster name to go to the cluster console, choose **Workloads** in the navigation pane, and click **Create Workload** in the upper right corner.
#. Configure **basic information** about the workload.

   +-------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter         | Description                                                                                                                                                                                                                     |
   +===================+=================================================================================================================================================================================================================================+
   | Workload Type     | Select **Cron Job**. For details about different workload types, see :ref:`Workload Overview <cce_10_0006>`.                                                                                                                    |
   +-------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Workload Name     | Enter a name for the workload. Enter 1 to 52 characters starting with a lowercase letter and ending with a lowercase letter or digit. Only lowercase letters, digits, and hyphens (-) are allowed.                              |
   +-------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Namespace         | Select a namespace for the workload. The default value is **default**. You can also click **Create Namespace** to create one. For details, see :ref:`Creating a Namespace <cce_10_0278>`.                                       |
   +-------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Container Runtime | A CCE standard cluster uses a common runtime by default, whereas a CCE Turbo cluster supports both common and secure runtimes. For details about their differences, see :ref:`Secure Runtime and Common Runtime <cce_10_0463>`. |
   +-------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Configure **container settings** for the workload.

   -  **Container Information**: Click **Add Container** on the right to configure multiple containers for the pod.

      .. caution::

         If you configured multiple containers for a pod, ensure that the ports used by each container do not conflict with each other, or the workload cannot be deployed.

      -  **Basic Info**: Configure basic information about the container.

         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                         |
         +===================================+=====================================================================================================================================================================================================================================================================================================================================================================================================================================+
         | Container Name                    | Enter a name for the container.                                                                                                                                                                                                                                                                                                                                                                                                     |
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
         |                                   | For details about how to use GPUs in a cluster, see :ref:`Default GPU Scheduling in Kubernetes <cce_10_0345>`.                                                                                                                                                                                                                                                                                                                      |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | (Optional) Privileged Container   | Programs in a privileged container have certain privileges. If this option is enabled, the container will be assigned privileges. For example, privileged containers can manipulate network devices on the host machine, modify kernel parameters, access all devices on the node.                                                                                                                                                  |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | For more information, see `Pod Security Standards <https://kubernetes.io/docs/concepts/security/pod-security-standards/>`__.                                                                                                                                                                                                                                                                                                        |
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

      -  (Optional) **Lifecycle**: Configure operations to be performed in a specific phase of the container lifecycle, such as Startup Command, Post-Start, and Pre-Stop. For details, see :ref:`Configuring the Container Lifecycle <cce_10_0105>`.
      -  (Optional) **Environment Variables**: Configure variables for the container running environment using key-value pairs. These variables transfer external information to containers running in pods and can be flexibly modified after application deployment. For details, see :ref:`Configuring Environment Variables <cce_10_0113>`.

   -  **Image Access Credential**: Select the credential used for accessing the image repository. The default value is **default-secret**. You can use default-secret to access images in SWR Shared Edition. For details about **default-secret**, see :ref:`default-secret <cce_10_0388__section11760122012591>`.
   -  (Optional) **GPU**: **All** is selected by default. The workload instance will be scheduled to the node of the specified GPU type.

#. Configure **execution settings**.

   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
   +===================================+=================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================+
   | Concurrency Policy                | The following modes are supported:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
   |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
   |                                   | -  **Forbid**: A new job can be created only after the previous one is complete.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
   |                                   | -  **Allow**: The CronJob allows concurrently running jobs, which preempt cluster resources.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
   |                                   | -  **Replace**: If it is time for a new job to run but the previous job has not completed yet, the CronJob replaces the currently running job with a new one.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Policy Settings                   | Specify when a new CronJob is executed. Policy settings in YAML are implemented using cron expressions.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
   |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
   |                                   | -  The CronJob is executed at a fixed interval. The unit can be minute, hour, day, or month. For example, if a CronJob is executed every 30 minutes, its cron expression is **\*/30 \* \* \* \***. The job will be executed at 30-minute intervals, starting from the top of the hour, for example, **00:00:00**, **00:30:00**, **01:00:00**, and **...**.                                                                                                                                                                                                                                                                                                      |
   |                                   | -  The CronJob is executed at a fixed time (by month). For example, if a CronJob is executed at 00:00 on the first day of each month, its cron expression is **0 0 1 \*/1 \***. The job will be executed at **\****-01-01 00:00:00**, **\****-02-01 00:00:00**, and **...**.                                                                                                                                                                                                                                                                                                                                                                                    |
   |                                   | -  The CronJob is executed by week. For example, if a CronJob is executed at 00:00 every Monday, its cron expression is **0 0 \* \* 1**. The job will be executed at **\****-**-01 00:00:00 on Monday**, **\****-**-08 00:00:00 on Monday**, and **...**.                                                                                                                                                                                                                                                                                                                                                                                                       |
   |                                   | -  **Custom Cron Expression**: For details about how to use cron expressions, see `CronJob <https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/#cron-schedule-syntax>`__.                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
   |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
   |                                   | .. note::                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
   |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
   |                                   |    -  If a CronJob is executed at a fixed time (by month) and the number of days in a month does not exist, the CronJob will not be executed in this month. For example, the execution will skip February if the date is set to 30.                                                                                                                                                                                                                                                                                                                                                                                                                             |
   |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
   |                                   |    -  Due to the definition of cron, the fixed period is not a strict period. The time unit range is divided from 0 by period. For example, if the unit is minute, the value ranges from 0 to 59. If the value cannot be exactly divided, the last period is reset. Therefore, an accurate period can be represented only when the period can be evenly divided.                                                                                                                                                                                                                                                                                                |
   |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
   |                                   |       Take a CronJob that runs hourly as an example. If an interval that can divide 24 hours exactly, such as **/2, /3, /4, /6, /8, and /12**, the period can be accurately represented. However, if a different period is used, the last period will reset at the beginning of each new day. For example, if the cron expression is **\* \*/12 \* \* \***, the task will run at **00:00:00** and **12:00:00** every day. Similarly, if the cron expression is **\* \*/13 \* \* \***, the task will run at **00:00:00** and **13:00:00** every day. Note that the last period will reset at 00:00:00 on the following day, even if it has not reached 13 hours. |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Time Zone                         | Specify the time zone where the policy settings are applied. If this parameter is not specified, the time zone of the master node will be used by default.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Job Records                       | Configure the number of successful or failed jobs that are reserved. If the value is set to **0**, no jobs will be reserved once they finish or fail.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. (Optional) Configure **advanced settings** for the workload.

   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                             |
   +===================================+=========================================================================================================================================================================================================================================================================================================================================================================================================================+
   | Labels and Annotations            | Add labels or annotations for pods using key-value pairs. After the setting, click **Confirm**. For details about labels and annotations, see :ref:`Configuring Labels and Annotations <cce_10_0386>`.                                                                                                                                                                                                                  |
   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Network Configuration             | -  Pod ingress/egress bandwidth limit: You can set ingress and egress bandwidth limits for pods. For details, see :ref:`Configuring QoS for a Pod <cce_10_0382>`.                                                                                                                                                                                                                                                       |
   |                                   | -  Whether to enable a specified container network configuration: available only for clusters that support this function. After you enable a specified container network configuration, the workload will use the subnet and security group defined in the configuration. For details, see :ref:`Binding a Subnet and Security Group to a Namespace or Workload Using a Container Network Configuration <cce_10_0196>`. |
   |                                   | -  Specify the container network configuration name: Only the custom container network configuration whose associated resource type is workload can be selected.                                                                                                                                                                                                                                                        |
   |                                   | -  IPv6 shared bandwidth: available only for clusters that support this function. After this function is enabled, you can configure a shared bandwidth for a pod with IPv6 dual-stack network interfaces. For details, see :ref:`Configuring a Shared Bandwidth for Dual-Stack Pods in a CCE Turbo Cluster <cce_10_0604>`.                                                                                              |
   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

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
     schedule: "*/1 * * * *"  # The job is executed every minute.
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

   Command output:

   .. code-block::

      NAME    SCHEDULE      TIMEZONE   SUSPEND   ACTIVE   LAST SCHEDULE   AGE
      hello   */1 * * * *   <none>     False     0        59s             2m36s

#. Check the job that is started at a scheduled time.

   .. code-block::

      kubectl get jobs

   Command output:

   .. code-block::

      NAME               COMPLETIONS   DURATION   AGE
      hello-1597387980   1/1           27s        45s

#. Check the pods started by the job.

   .. code-block::

      kubectl get pod

   Command output:

   .. code-block::

      NAME                           READY     STATUS      RESTARTS   AGE
      hello-1597387980-tjv8f         0/1       Completed   0          114s

#. Check pod logs. It is expected that "Hello from the Kubernetes cluster" is displayed in the logs.

   .. code-block::

      kubectl logs hello-1597387980-tjv8f

   Command output:

   .. code-block::

      Fri Aug 14 06:56:31 UTC 2020
      Hello from the Kubernetes cluster

#. Delete the CronJob. When a CronJob is deleted, its associated tasks and pods are deleted accordingly.

   .. code-block::

      kubectl delete cronjob hello

   Command output:

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
   | Deleting a CronJob                | #. Select the target CronJob and choose **More** > **Delete** in the **Operation** column.        |
   |                                   |                                                                                                   |
   |                                   | #. Click **Yes**.                                                                                 |
   |                                   |                                                                                                   |
   |                                   |    Deleted jobs cannot be restored. Exercise caution when deleting a job.                         |
   +-----------------------------------+---------------------------------------------------------------------------------------------------+
