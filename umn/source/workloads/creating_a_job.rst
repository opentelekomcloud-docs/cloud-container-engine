:original_name: cce_01_0150.html

.. _cce_01_0150:

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

Resources have been created. For details, see :ref:`Creating a Node <cce_01_0033>`. If clusters and nodes are available, you need not create them again.

Procedure
---------

#. (Optional) If you use a private container image to create your job, upload the container image to the image repository.

#. Log in to the CCE console. In the navigation pane, choose **Workloads** > **Jobs**. Click **Create Job**.

#. Configure the basic job information listed in :ref:`Table 1 <cce_01_0150__t70ce3a99637a44ce8f7274857fb245b1>`. The parameters marked with an asterisk (*) are mandatory.

   .. _cce_01_0150__t70ce3a99637a44ce8f7274857fb245b1:

   .. table:: **Table 1** Basic job information

      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                              |
      +===================================+==========================================================================================================================================================================+
      | \* Job Name                       | Name of a new job. The name must be unique.                                                                                                                              |
      |                                   |                                                                                                                                                                          |
      |                                   | Enter 4 to 63 characters starting with a lowercase letter and ending with a letter or digit. Only lowercase letters, digits, and hyphens (-) are allowed.                |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Cluster                        | Cluster to which a new job belongs.                                                                                                                                      |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Namespace                      | Namespace to which the new job belongs. By default, this parameter is set to **default**.                                                                                |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \*Instances                       | Number of pods in this job. A job can have one or more pods. You can specify the number of pods. The default value is **1**.                                             |
      |                                   |                                                                                                                                                                          |
      |                                   | Each job pod consists of the same containers. Configuring multiple job pods can ensure high availability. The job can continue to run even if one of the pods is faulty. |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Description                       | Description of a job.                                                                                                                                                    |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Next: Add Container** to add a container and an image.

   a. Click **Select Container Image** to select the image to be deployed.

      -  **My Images**: displays all image repositories you created.
      -  **Third-Party Images**: Create a job using an image from any third-party image repository. When you create a job using a third-party image, ensure that the node where the job is running can access public networks. For details about how to use a third-party image, see :ref:`Using a Third-Party Image <cce_01_0009>`.

         -  If your image repository does not require authentication, set **Secret Authentication** to **No**, enter an image address in **Image Address**, and then click **OK**.
         -  If your image repository must be authenticated (account and password), you need to create a secret and then use a third-party image. For details, see :ref:`Using a Third-Party Image <cce_01_0009>`.

      -  **Shared Images**: The images shared by other tenants using the SWR service are displayed here. You can create workloads based on the shared images.

   b. Set image parameters.

      .. table:: **Table 2** Image parameters

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

      .. table:: **Table 3** Advanced settings

         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                                                                                                       |
         +===================================+===================================================================================================================================================================================================================================================================================+
         | Lifecycle                         | Lifecycle scripts define the actions taken for container-related jobs when a lifecycle event occurs.                                                                                                                                                                              |
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
         | Data Storage                      | The local disk or cloud storage can be mounted to a container to implement persistent data file storage.                                                                                                                                                                          |
         |                                   |                                                                                                                                                                                                                                                                                   |
         |                                   | For details, see :ref:`Storage (CSI) <cce_01_0042>`.                                                                                                                                                                                                                              |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Log Policies                      | Set a log policy and log path for collecting workload logs and preventing logs from being over-sized. For details, see :ref:`Container Logs <cce_01_0018>`.                                                                                                                       |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   d. (Optional) One job pod contains one or more related containers. If your job contains multiple containers, click **Add Container** to add containers.

#. Click **Create**.

   If the status of the job is **Executing**, the job has been created successfully.

.. _cce_01_0150__section450152719412:

Using kubectl
-------------

A job has the following configuration parameters:

-  **spec.template**: has the same schema as a pod.
-  **RestartPolicy**: can only be set to **Never** or **OnFailure**.
-  For a single-pod job, the job ends after the pod runs successfully by default.
-  **.spec.completions**: indicates the number of pods that need to run successfully to end a job. The default value is **1**.
-  **.spec.parallelism**: indicates the number of pods that run concurrently. The default value is **1**.
-  **spec.backoffLimit**: indicates the maximum number of retries performed if a pod fails. When the limit is reached, the pod will not try again.
-  **.spec.activeDeadlineSeconds**: indicates the running time of pods. Once the time is reached, all pods of the job are terminated. The priority of .spec.activeDeadlineSeconds is higher than that of .spec.backoffLimit. That is, if a job reaches the .spec.activeDeadlineSeconds, the spec.backoffLimit is ignored.

Based on the **.spec.completions** and **.spec.Parallelism** settings, jobs are classified into the following types.

.. table:: **Table 4** Job types

   +---------------------------------------------+-----------------------------------------------------------------------+-------------------------------------------------------+
   | Job Type                                    | Description                                                           | Example                                               |
   +=============================================+=======================================================================+=======================================================+
   | One-off jobs                                | A single pod runs once until successful termination.                  | Database migration                                    |
   +---------------------------------------------+-----------------------------------------------------------------------+-------------------------------------------------------+
   | Jobs with a fixed completion count          | One pod runs until reaching the specified **completions** count.      | Work queue processing pod                             |
   +---------------------------------------------+-----------------------------------------------------------------------+-------------------------------------------------------+
   | Parallel jobs with a fixed completion count | Multiple pods run until reaching the specified **completions** count. | Multiple pods for processing work queues concurrently |
   +---------------------------------------------+-----------------------------------------------------------------------+-------------------------------------------------------+
   | Parallel jobs                               | One or more pods run until successful termination.                    | Multiple pods for processing work queues concurrently |
   +---------------------------------------------+-----------------------------------------------------------------------+-------------------------------------------------------+

The following is an example job, which calculates Pi till the 2000\ :sup:`th` digit and prints the output.

.. code-block::

   apiVersion: batch/v1
   kind: Job
   metadata:
     name: myjob
   spec:
     completions: 50        # 50 pods need to be run to finish a job. In this example, Pi is printed for 50 times.
     parallelism: 5        # 5 pods are run in parallel.
     backoffLimit: 5        # The maximum number of retry times is 5.
     template:
       spec:
         containers:
         - name: pi
           image: perl
           command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
         restartPolicy: Never

**Description**

-  **apiVersion: batch/v1** indicates the version of the current job.
-  **kind: Job** indicates that the current resource is a job.
-  **restartPolicy: Never** indicates the current restart policy. For jobs, this parameter can only be set to **Never** or **OnFailure**. For other controllers (for example, Deployments), you can set this parameter to **Always**.

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

#. Query the pod status.

   **kubectl get pod**

   .. code-block:: console

      [root@k8s-master k8s]# kubectl get pod
      NAME          READY   STATUS      RESTARTS   AGE
      myjob-29qlw   0/1     Completed   0          4m5s
      ...

   If the status is **Completed**, the job is complete.

#. View the pod logs.

   **kubectl logs**

   .. code-block::

      # kubectl logs myjob-29qlw
      3.1415926535897932384626433832795028841971693993751058209749445923078164062862089986280348253421170679821480865132823066470938446095505822317253594081284811174502841027019385211055596446229489549303819644288109756659334461284756482337867831652712019091456485669234603486104543266482133936072602491412737245870066063155881748815209209628292540917153643678925903600113305305488204665213841469519415116094330572703657595919530921861173819326117931051185480744623799627495673518857527248912279381830119491298336733624406566430860213949463952247371907021798609437027705392171762931767523846748184676694051320005681271452635608277857713427577896091736371787214684409012249534301465495853710507922796892589235420199561121290219608640344181598136297747713099605187072113499999983729780499510597317328160963185950244594553469083026425223082533446850352619311881710100031378387528865875332083814206171776691473035982534904287554687311595628638823537875937519577818577805321712268066130019278766111959092164201989380952572010654858632788659361533818279682303019520353018529689957736225994138912497217752834791315155748572424541506959508295331168617278558890750983817546374649393192550604009277016711390098488240128583616035637076601047101819429555961989467678374494482553797747268471040475346462080466842590694912933136770289891521047521620569660240580381501935112533824300355876402474964732639141992726042699227967823547816360093417216412199245863150302861829745557067498385054945885869269956909272107975093029553211653449872027559602364806654991198818347977535663698074265425278625518184175746728909777727938000816470600161452491921732172147723501414419735685481613611573525521334757418494684385233239073941433345477624168625189835694855620992192221842725502542568876717904946016534668049886272327917860857843838279679766814541009538837863609506800642251252051173929848960841284886269456042419652850222106611863067442786220391949450471237137869609563643719172874677646575739624138908658326459958133904780275901

Related Operations
------------------

After a one-off job is created, you can perform operations listed in :ref:`Table 5 <cce_01_0150__t84075653e7544394939d13740fad0c20>`.

.. _cce_01_0150__t84075653e7544394939d13740fad0c20:

.. table:: **Table 5** Other operations

   +-----------------------------------+--------------------------------------------------------------------------------------------------+
   | Operation                         | Description                                                                                      |
   +===================================+==================================================================================================+
   | Viewing a YAML                    | Click **View YAML** next to the job name to view the YAML file corresponding to the current job. |
   +-----------------------------------+--------------------------------------------------------------------------------------------------+
   | Deleting a one-off job            | #. Select the job to be deleted and click **Delete** in the **Operation** column.                |
   |                                   |                                                                                                  |
   |                                   | #. Click **OK**.                                                                                 |
   |                                   |                                                                                                  |
   |                                   |    Deleted jobs cannot be restored. Exercise caution when deleting a job.                        |
   +-----------------------------------+--------------------------------------------------------------------------------------------------+
