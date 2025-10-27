:original_name: cce_10_0150.html

.. _cce_10_0150:

Creating a Job
==============

A `job <https://kubernetes.io/docs/concepts/workloads/controllers/job/>`__ is a Kubernetes workload designed for managing batch processing tasks, handling one-time or short-lived tasks. Unlike long-running services managed by Deployments or StatefulSets, a job creates one or more pods, executing them with defined start and end phases. If the task is not completed, the job retries pod execution until the target is reached.

You can set different tasks for a job as required.

-  One-off job: A job creates only one pod to execute a task. If the task is successfully completed, the pod exits (**exit 0**).
-  Job with fixed completion counts: A job must have multiple pods successfully executed tasks. When a specified number of successful pod completions are reached, the task is complete.
-  Job with a work queue: A job processes tasks in a work queue. Each pod is allocated with an index. If each of these pods is successfully completed, the job is finished.

Prerequisites
-------------

-  A cluster is available. For details about how to create a cluster, see :ref:`Creating a CCE Standard/Turbo Cluster <cce_10_0028>`.
-  There are some available nodes in the cluster. If no node is available, create one by referring to :ref:`Creating a Node <cce_10_0363>`.

Using the CCE Console
---------------------

#. Log in to the CCE console.

#. Click the cluster name to go to the cluster console, choose **Workloads** in the navigation pane, and click the **Create Workload** in the upper right corner.

#. Configure **basic information** about the workload.

   +-------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter         | Description                                                                                                                                                                                                                     |
   +===================+=================================================================================================================================================================================================================================+
   | Workload Type     | Select **Job**. For details about different workload types, see :ref:`Workload Overview <cce_10_0006>`.                                                                                                                         |
   +-------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Workload Name     | Enter a name for the workload. Enter 1 to 63 characters starting with a lowercase letter and ending with a lowercase letter or digit. Only lowercase letters, digits, and hyphens (-) are allowed.                              |
   +-------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Namespace         | Select a namespace for the workload. The default value is **default**. You can also click **Create Namespace** to create one. For details, see :ref:`Creating a Namespace <cce_10_0278>`.                                       |
   +-------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Pods              | Enter the number of workload pods.                                                                                                                                                                                              |
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

      -  (Optional) **Data Storage**: Mount local storage or cloud storage to the container. The application scenarios and mounting modes vary with the StorageClass. For details, see :ref:`Storage <cce_10_0374>`.

         .. note::

            If the workload contains more than one pod, EVS volumes cannot be mounted.

      -  (Optional) **Logging**: Report standard container output logs to AOM by default, without requiring manual settings. You can manually configure the log collection path. For details, see :ref:`Collecting Container Logs Using ICAgent <cce_10_0018>`.

         To disable the collection of the standard output logs of the current workload, add the annotation **kubernetes.AOM.log.stdout: []** in :ref:`Labels and Annotations <cce_10_0150__li9923141863518>` in the **Advanced Settings** area. For details about how to use this annotation, see :ref:`Table 1 <cce_10_0386__table19757101213714>`.

   -  **Image Access Credential**: Select the credential used for accessing the image repository. The default value is **default-secret**. You can use default-secret to access images in SWR Shared Edition. For details about **default-secret**, see :ref:`default-secret <cce_10_0388__section11760122012591>`.
   -  (Optional) **GPU**: **All** is selected by default. The workload instance will be scheduled to the node of the specified GPU type.

#. .. _cce_10_0150__li9923141863518:

   (Optional) Configure **advanced settings** for the workload.

   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                            |
   +===================================+========================================================================================================================================================================================================================================================================================================================================================================================================================================+
   | Labels and Annotations            | Add labels or annotations for pods using key-value pairs. After the setting, click **Confirm**. For details about labels and annotations, see :ref:`Configuring Labels and Annotations <cce_10_0386>`.                                                                                                                                                                                                                                 |
   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Job Settings                      | -  **Parallel Pods**: Maximum number of pods that can run in parallel during job execution. The value cannot be greater than the total number of pods in the job.                                                                                                                                                                                                                                                                      |
   |                                   | -  **Timeout (s)**: Once a job reaches this time, the job status becomes failed and all pods in this job will be deleted. If you leave this parameter blank, the job will never time out.                                                                                                                                                                                                                                              |
   |                                   | -  Completion Mode                                                                                                                                                                                                                                                                                                                                                                                                                     |
   |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                        |
   |                                   |    -  **Non-indexed**: A job is considered complete when all the pods are successfully executed. Each pod completion is homologous to each other.                                                                                                                                                                                                                                                                                      |
   |                                   |    -  **Indexed**: Each pod gets an associated completion index from 0 to the number of pods minus 1. The job is considered complete when every pod allocated with an index is successfully executed. For an indexed job, pods are named in the format of $(job-name)-$(index).                                                                                                                                                        |
   |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                        |
   |                                   | -  **Suspend Job**: By default, a job is executed immediately after being created. The job's execution will be suspended if you enable this option, and resumed after you disable it.                                                                                                                                                                                                                                                  |
   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Network Configuration             | -  Pod ingress/egress bandwidth limit: You can set ingress and egress bandwidth limits for pods. For details, see :ref:`Configuring QoS for a Pod <cce_10_0382>`.                                                                                                                                                                                                                                                                      |
   |                                   | -  Whether to enable a specified container network configuration: available only for clusters that support this function. After you enable a specified container network configuration, the workload will be created using the container subnet and security group in the configuration. For details, see :ref:`Binding a Subnet and Security Group to a Namespace or Workload Using a Container Network Configuration <cce_10_0196>`. |
   |                                   | -  Specify the container network configuration name: Only the custom container network configuration whose associated resource type is workload can be selected.                                                                                                                                                                                                                                                                       |
   |                                   | -  IPv6 shared bandwidth: available only for clusters that support this function. After this function is enabled, you can configure a shared bandwidth for a pod with IPv6 dual-stack network interfaces. For details, see :ref:`Configuring Shared Bandwidth for a Pod with IPv6 Dual-Stack Network Interfaces <cce_10_0604>`.                                                                                                        |
   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Create Workload** in the lower right corner. After a period of time, when the workload enters the processing state, tasks will be processed sequentially. After all tasks are processed, the workload status changes to **Executed**.

.. _cce_10_0150__section450152719412:

Using kubectl
-------------

A job has the following configuration parameters:

-  **.spec.completions**: indicates the number of pods that need to run successfully to end a job. The default value is **1**.
-  **.spec.parallelism**: indicates the number of pods that run concurrently. The default value is **1**.
-  **.spec.backoffLimit**: indicates the maximum number of retries performed if a pod fails. When the limit is reached, the pod will not try again.
-  **.spec.activeDeadlineSeconds**: indicates the running time of pods. Once the time is reached, the job and its pods are terminated. **.spec.activeDeadlineSeconds** has a higher priority than **.spec.backoffLimit**. If a job reaches **.spec.activeDeadlineSeconds**, **spec.backoffLimit** is ignored.

Based on the **.spec.completions** and **.spec.parallelism** settings, jobs are classified into the following types.

.. table:: **Table 1** Job types

   +---------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------+-------------------+
   | Job Type                                    | Description                                                                                                                                                                                                                                                                                                                             | .spec.completions           | .spec.parallelism |
   +=============================================+=========================================================================================================================================================================================================================================================================================================================================+=============================+===================+
   | One-off jobs                                | A job creates one pod until it successfully completes.                                                                                                                                                                                                                                                                                  | 1                           | 1                 |
   +---------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------+-------------------+
   | Jobs with a fixed completion count          | A job creates one pod in sequence and is complete when the number of successful pods reaches the value of **.spec.completions**.                                                                                                                                                                                                        | >1                          | 1                 |
   +---------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------+-------------------+
   | Parallel jobs with a fixed completion count | A job creates multiple pods in sequence and is complete when the number of successful pods reaches the value of **.spec.completions**.                                                                                                                                                                                                  | >1                          | >1                |
   +---------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------+-------------------+
   | Parallel jobs with a work queue             | A job creates one or more pods. Each pod takes one task from the message queue, processes it, and repeats until the end of the queue is reached. Then the pod deletes the task and exits. For details, see `Fine Parallel Processing Using a Work Queue <https://kubernetes.io/docs/tasks/job/fine-parallel-processing-work-queue/>`__. | Leave this parameter blank. | >1 or =1          |
   +---------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------+-------------------+

The following is an example job, which calculates Pi till the 2000\ :sup:`th` digit and prints the output.

.. code-block::

   apiVersion: batch/v1
   kind: Job
   metadata:
     name: myjob
   spec:
     completions: 50        # A total of 50 pods need to run to finish a job. In this example, Pi is printed for 50 times.
     parallelism: 5        # A total of 5 pods run in parallel.
     backoffLimit: 5        # A maximum of 5 retries is allowed.
     template:
       spec:
         containers:
         - name: pi
           image: perl
           command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
         restartPolicy: Never     # For a job, set this parameter to Never or OnFailure. For other controllers (such as Deployments), set this parameter to Always.
         imagePullSecrets:
           - name: default-secret

**Run the job.**

#. Start the job.

   .. code-block::

      kubectl apply -f myjob.yaml

   Command output:

   .. code-block::

      job.batch/myjob created

#. View the job details.

   .. code-block::

      kubectl get job

   Command output:

   .. code-block::

      NAME    COMPLETIONS   DURATION   AGE
      myjob   50/50         23s        3m45s

   If the value of **COMPLETIONS** is **50/50**, the job is successfully executed.

#. View the pod status.

   .. code-block::

      kubectl get pod

   Command output:

   .. code-block::

      NAME          READY   STATUS      RESTARTS   AGE
      myjob-29qlw   0/1     Completed   0          4m5s
      ...

   If the status is **Completed**, the job is complete.

#. View the pod logs.

   .. code-block::

      kubectl logs myjob-29qlw

Related Operations
------------------

After a one-off job is created, you can perform operations listed in :ref:`Table 2 <cce_10_0150__t84075653e7544394939d13740fad0c20>`.

.. _cce_10_0150__t84075653e7544394939d13740fad0c20:

.. table:: **Table 2** Other operations

   +-----------------------------------+----------------------------------------------------------------------------------------------------------------+
   | Operation                         | Description                                                                                                    |
   +===================================+================================================================================================================+
   | Viewing a YAML file               | Locate the row containing the target job and choose **More** > **View YAML** to view the YAML file of the job. |
   +-----------------------------------+----------------------------------------------------------------------------------------------------------------+
   | Deleting a job                    | #. Select the target job and choose **More** > **Delete** in the **Operation** column.                         |
   |                                   |                                                                                                                |
   |                                   | #. Click **Yes**.                                                                                              |
   |                                   |                                                                                                                |
   |                                   |    Deleted jobs cannot be restored. Exercise caution when deleting a job.                                      |
   +-----------------------------------+----------------------------------------------------------------------------------------------------------------+
