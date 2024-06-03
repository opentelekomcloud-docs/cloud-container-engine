:original_name: cce_10_0150.html

.. _cce_10_0150:

Creating a Job
==============

Scenario
--------

Jobs are short-lived and run for a certain time to completion. They can be executed immediately after being deployed. It is completed after it exits normally (exit 0).

A job is a resource object that is used to control batch tasks. It is different from a long-term servo workload (such as Deployment and StatefulSet).

A job is started and terminated at specific times, while a long-term servo workload runs unceasingly unless being terminated. The pods managed by a job automatically exit after successfully completing the job based on user configurations. The success flag varies according to the spec.completions policy.

-  One-off jobs: A single pod runs once until successful termination.
-  Jobs with a fixed success count: N pods run until successful termination.
-  A queue job is considered completed based on the global success confirmed by the application.

Prerequisites
-------------

Resources have been created. For details, see :ref:`Creating a Node <cce_10_0363>`. If clusters and nodes are available, you need not create them again.

Using the CCE Console
---------------------

#. Log in to the CCE console.

#. Click the cluster name to go to the cluster console, choose **Workloads** in the navigation pane, and click the **Create Workload** in the upper right corner.

#. Set basic information about the workload.

   **Basic Info**

   -  **Workload Type**: Select **Job**. For details about workload types, see :ref:`Overview <cce_10_0006>`.
   -  **Workload Name**: Enter the name of the workload. Enter 1 to 63 characters starting with a lowercase letter and ending with a lowercase letter or digit. Only lowercase letters, digits, and hyphens (-) are allowed.
   -  **Namespace**: Select the namespace of the workload. The default value is **default**. You can also click **Create Namespace** to create one. For details, see :ref:`Creating a Namespace <cce_10_0278>`.
   -  **Pods**: Enter the number of pods of the workload.
   -  **Container Runtime**: A CCE standard cluster uses runC by default, whereas a CCE Turbo cluster supports both runC and Kata. For details about the differences, see :ref:`Kata Runtime and Common Runtime <cce_10_0463>`.

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
         |                                   | To use a third-party image, see :ref:`Using Third-Party Images <cce_10_0009>`.                                                                                                                                                                                                                                                                                                                                                      |
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
         | (Optional) GPU Quota              | Configurable only when the cluster contains GPU nodes and the :ref:`CCE AI Suite (NVIDIA GPU) <cce_10_0141>` add-on is installed.                                                                                                                                                                                                                                                                                                   |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | -  **All**: No GPU will be used.                                                                                                                                                                                                                                                                                                                                                                                                    |
         |                                   | -  **Dedicated**: GPU resources are dedicated for the container.                                                                                                                                                                                                                                                                                                                                                                    |
         |                                   | -  **Shared**: percentage of GPU resources used by the container. For example, if this parameter is set to **10%**, the container uses 10% of GPU resources.                                                                                                                                                                                                                                                                        |
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

      -  (Optional) **Lifecycle**: Configure operations to be performed in a specific phase of the container lifecycle, such as Startup Command, Post-Start, and Pre-Stop. For details, see :ref:`Configuring Container Lifecycle Parameters <cce_10_0105>`.

      -  (Optional) **Environment Variables**: Configure variables for the container running environment using key-value pairs. These variables transfer external information to containers running in pods and can be flexibly modified after application deployment. For details, see :ref:`Configuring Environment Variables <cce_10_0113>`.

      -  (Optional) **Data Storage**: Mount local storage or cloud storage to the container. The application scenarios and mounting modes vary with the storage type. For details, see :ref:`Storage <cce_10_0374>`.

         .. note::

            If the workload contains more than one pod, EVS volumes cannot be mounted.

      -  (Optional) **Logging**: Report standard container output logs to AOM by default, without requiring manual settings. You can manually configure the log collection path. For details, see :ref:`Collecting Container Logs Using ICAgent <cce_10_0018>`.

         To disable the standard output of the current workload, add the annotation **kubernetes.AOM.log.stdout: []** in :ref:`Labels and Annotations <cce_10_0047__li179714209414>`. For details about how to use this annotation, see :ref:`Table 1 <cce_10_0386__table194691458405>`.

   -  **Image Access Credential**: Select the credential used for accessing the image repository. The default value is **default-secret**. You can use default-secret to access images in SWR. For details about **default-secret**, see :ref:`default-secret <cce_10_0388__section11760122012591>`.

   -  (Optional) **GPU**: **All** is selected by default. The workload instance will be scheduled to the node of the specified GPU type.

   **(Optional) Advanced Settings**

   -  **Labels and Annotations**: Add labels or annotations for pods using key-value pairs. After entering the key and value, click **Confirm**. For details about how to use and configure labels and annotations, see :ref:`Labels and Annotations <cce_10_0386>`.

   -  **Job Settings**

      -  **Parallel Pods**: Maximum number of pods that can run in parallel during job execution. The value cannot be greater than the total number of pods in the job.
      -  **Timeout (s)**: Once a job reaches this time, the job status becomes failed and all pods in this job will be deleted. If you leave this parameter blank, the job will never time out.
      -  Completion Mode

         -  **Non-indexed**: A job is considered complete when all the pods are successfully executed. Each pod completion is homologous to each other.
         -  **Indexed**: Each pod gets an associated completion index from 0 to the number of pods minus 1. The job is considered complete when every pod allocated with an index is successfully executed. For an indexed job, pods are named in the format of $(job-name)-$(index).

      -  **Suspend Job**: By default, a job is executed immediately after being created. The job's execution will be suspended if you enable this option, and resumed after you disable it.

   -  **Network Configuration**

      -  Pod ingress/egress bandwidth limitation: You can set ingress/egress bandwidth limitation for pods. For details, see :ref:`Configuring QoS for a Pod <cce_10_0382>`.
      -  IPv6 shared bandwidth: available only for clusters that support this function. After this function is enabled, you can configure a shared bandwidth for a pod with IPv6 dual-stack ENIs. For details, see :ref:`Configuring Shared Bandwidth for a Pod with IPv6 Dual-Stack ENIs <cce_10_0604>`.

#. Click **Create Workload** in the lower right corner.

.. _cce_10_0150__section450152719412:

Using kubectl
-------------

A job has the following configuration parameters:

-  **.spec.completions**: indicates the number of pods that need to run successfully to end a job. The default value is **1**.
-  **.spec.parallelism**: indicates the number of pods that run concurrently. The default value is **1**.
-  **.spec.backoffLimit**: indicates the maximum number of retries performed if a pod fails. When the limit is reached, the pod will not try again.
-  **.spec.activeDeadlineSeconds**: indicates the running time of pods. Once the time is reached, all pods of the job are terminated. The priority of .spec.activeDeadlineSeconds is higher than that of .spec.backoffLimit. That is, if a job reaches the .spec.activeDeadlineSeconds, the spec.backoffLimit is ignored.

Based on the **.spec.completions** and **.spec.parallelism** settings, jobs are classified into the following types.

.. table:: **Table 1** Job types

   +---------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------+-------------------+
   | Job Type                                    | Description                                                                                                                                                                                                                                                                                                                              | .spec.completions           | .spec.parallelism |
   +=============================================+==========================================================================================================================================================================================================================================================================================================================================+=============================+===================+
   | One-off jobs                                | A job creates one pod until it successfully completes.                                                                                                                                                                                                                                                                                   | 1                           | 1                 |
   +---------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------+-------------------+
   | Jobs with a fixed completion count          | A job creates one pod in sequence and is complete when the number of successful pods reaches the value of **.spec.completions**.                                                                                                                                                                                                         | >1                          | 1                 |
   +---------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------+-------------------+
   | Parallel jobs with a fixed completion count | A job creates multiple pods in sequence and is complete when the number of successful pods reaches the value of **.spec.completions**.                                                                                                                                                                                                   | >1                          | >1                |
   +---------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------+-------------------+
   | Parallel jobs with a work queue             | A job creates one or more pods. Each pod takes one task from the message queue, processes it, and repeats until the end of the queue is reached. Then the pod deletes the task and exists. For details, see `Fine Parallel Processing Using a Work Queue <https://kubernetes.io/docs/tasks/job/fine-parallel-processing-work-queue/>`__. | Leave this parameter blank. | >1 or =1          |
   +---------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------+-------------------+

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

   .. code-block:: console

      [root@k8s-master k8s]# kubectl apply -f myjob.yaml
      job.batch/myjob created

#. View the job details.

   **kubectl get job**

   .. code-block:: console

      [root@k8s-master k8s]# kubectl get job
      NAME    COMPLETIONS   DURATION   AGE
      myjob   50/50         23s        3m45s

   If the value of **COMPLETIONS** is **50/50**, the job is successfully executed.

#. View the pod status.

   **kubectl get pod**

   .. code-block:: console

      [root@k8s-master k8s]# kubectl get pod
      NAME          READY   STATUS      RESTARTS   AGE
      myjob-29qlw   0/1     Completed   0          4m5s
      ...

   If the status is **Completed**, the job is complete.

#. View the pod logs.

   **kubectl logs <pod_name>**

   .. code-block::

      # kubectl logs myjob-29qlw
      3.1415926535897932384626433832795028841971693993751058209749445923078164062862089986280348253421170679821480865132823066470938446095505822317253594081284811174502841027019385211055596446229489549303819644288109756659334461284756482337867831652712019091456485669234603486104543266482133936072602491412737245870066063155881748815209209628292540917153643678925903600113305305488204665213841469519415116094330572703657595919530921861173819326117931051185480744623799627495673518857527248912279381830119491298336733624406566430860213949463952247371907021798609437027705392171762931767523846748184676694051320005681271452635608277857713427577896091736371787214684409012249534301465495853710507922796892589235420199561121290219608640344181598136297747713099605187072113499999983729780499510597317328160963185950244594553469083026425223082533446850352619311881710100031378387528865875332083814206171776691473035982534904287554687311595628638823537875937519577818577805321712268066130019278766111959092164201989380952572010654858632788659361533818279682303019520353018529689957736225994138912497217752834791315155748572424541506959508295331168617278558890750983817546374649393192550604009277016711390098488240128583616035637076601047101819429555961989467678374494482553797747268471040475346462080466842590694912933136770289891521047521620569660240580381501935112533824300355876402474964732639141992726042699227967823547816360093417216412199245863150302861829745557067498385054945885869269956909272107975093029553211653449872027559602364806654991198818347977535663698074265425278625518184175746728909777727938000816470600161452491921732172147723501414419735685481613611573525521334757418494684385233239073941433345477624168625189835694855620992192221842725502542568876717904946016534668049886272327917860857843838279679766814541009538837863609506800642251252051173929848960841284886269456042419652850222106611863067442786220391949450471237137869609563643719172874677646575739624138908658326459958133904780275901

Related Operations
------------------

After a one-off job is created, you can perform operations listed in :ref:`Table 2 <cce_10_0150__t84075653e7544394939d13740fad0c20>`.

.. _cce_10_0150__t84075653e7544394939d13740fad0c20:

.. table:: **Table 2** Other operations

   +-----------------------------------+-------------------------------------------------------------------------------------------------------------+
   | Operation                         | Description                                                                                                 |
   +===================================+=============================================================================================================+
   | Editing a YAML file               | Click **More** > **Edit YAML** next to the job name to edit the YAML file corresponding to the current job. |
   +-----------------------------------+-------------------------------------------------------------------------------------------------------------+
   | Deleting a job                    | #. Select the target job and choose **More** > **Delete** in the **Operation** column.                      |
   |                                   |                                                                                                             |
   |                                   | #. Click **Yes**.                                                                                           |
   |                                   |                                                                                                             |
   |                                   |    Deleted jobs cannot be restored. Exercise caution when deleting a job.                                   |
   +-----------------------------------+-------------------------------------------------------------------------------------------------------------+
