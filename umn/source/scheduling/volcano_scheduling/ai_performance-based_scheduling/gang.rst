:original_name: cce_10_0778.html

.. _cce_10_0778:

Gang
====

Gang scheduling is a scheduling algorithm that schedules correlated processes or threads to run simultaneously on different processors. It meets the scheduling requirements of "All or nothing" in the scheduling process and avoids the waste of cluster resources caused by arbitrary scheduling of pods. Gang is mainly used in scenarios that require multi-process collaboration, such as AI and big data scenarios. Gang scheduling effectively resolves pain points such as deadlocks in distributed training jobs, thereby significantly improving the utilization of cluster resources.

Prerequisites
-------------

-  A cluster of v1.19 or later is available. For details, see :ref:`Creating a CCE Standard/Turbo Cluster <cce_10_0028>`.
-  The Volcano add-on has been installed. For details, see :ref:`Volcano Scheduler <cce_10_0193>`.

How It Works
------------

The Gang scheduling policy is one of the core scheduling algorithms of Volcano. It meets the scheduling requirements of "All or nothing" in the scheduling process and avoids the waste of cluster resources caused by arbitrary scheduling of pods. The Gang scheduler algorithm checks whether the number of scheduled pods in a job meets the minimum requirements for running the job. If yes, all pods in the job will be scheduled. If no, the pods will not be scheduled.

The Gang scheduling algorithm based on container groups is well suitable for scenarios where multi-process collaboration is required. AI scenarios typically involve complex processes. Data ingestion, data analysts, data splitting, trainers, serving, and logging which require a group of containers to work together are suitable for container-based Gang scheduling. Multi-thread parallel computing communication scenarios under MPI computing framework are also suitable for Gang scheduling because master and slave processes need to work together. Containers in a pod group are highly correlated, and there may be resource contention. The overall scheduling allocation can effectively resolve deadlocks. If cluster resources are insufficient, Gang scheduling can significantly improve the utilization of cluster resources.

Configuring Gang
----------------

After Volcano is installed, you can enable or disable Gang scheduling on the **Scheduling** page. This function is enabled by default.

#. Log in to the CCE console.

#. Click the cluster name to access the cluster console. Choose **Settings** in the navigation pane and click the **Scheduling** tab.

#. In the **AI task performance enhanced scheduling** pane, select whether to enable Gang.

   This function helps you enhance the service throughput of the cluster and improve service running performance.

#. Click **Confirm**.

#. After the configuration, use Gang scheduling in workloads or Volcano jobs.

   -  Create a workload using Gang scheduling.

      a. Create a pod group and specify **minMember** and **minResources** as follows:

         .. code-block::

            apiVersion: scheduling.volcano.sh/v1beta1
            kind: PodGroup
            metadata:
              name: pg-test1
            spec:
              minMember: 3
              minResources:
                cpu: 3
                memory: 3Gi

         -  **minMember**: specifies the minimum requirement on the number of pods for running a workload. When the number of pods in the current pod group meets the requirement, these pods can be centrally scheduled.
         -  **minResources**: specifies the minimum requirement on resources for running a workload. When the available resources in a cluster meet the requirement, the group of pods can be centrally scheduled.

      b. When creating a workload, use **schedulerName** to specify Volcano Scheduler and **annotation** to specify the pod group in which Volcano Scheduler runs.

         .. code-block::

            apiVersion: apps/v1
            kind: Deployment
            metadata:
              name: podgroup-test
              labels:
                app: podgroup-test
            spec:
              replicas: 6
              selector:
                matchLabels:
                  app: podgroup-test
              template:
                metadata:
                  annotations:
                    scheduling.k8s.io/group-name: pg-test1
                  labels:
                    app: podgroup-test
                spec:
                  schedulerName: volcano
                  containers:
                  - name: test
                    image: busybox
                    imagePullPolicy: IfNotPresent
                    command: ['sh', '-c', 'echo "Hello, Kubernetes!" && sleep 3600']
                    resources:
                      requests:
                        cpu: 500m
                      limits:
                        cpu: 500m

         -  **schedulerName**: Set this parameter to **volcano**, indicating that Volcano will be used to schedule pods for the workload.
         -  **scheduling.k8s.io/group-name**: specifies the pod group created in the previous step, for example, **pg-test1**.

   -  Create a Volcano job using Gang scheduling.

      When creating a Volcano job, you only need to configure **minAvailable** and set **schedulerName** to **volcano**. Volcano Scheduler will automatically create a pod group and manage it. The following shows an example:

      .. code-block::

         apiVersion: batch.volcano.sh/v1alpha1
         kind: Job
         metadata:
           name: vcjob
         spec:
           schedulerName: volcano
           minAvailable: 2
           tasks:
             - replicas: 4
               name: "test"
               template:
                 spec:
                   containers:
                     - image: alpine
                       command: ["/bin/sh", "-c", "sleep 1000"]
                       imagePullPolicy: IfNotPresent
                       name: running
                       resources:
                         requests:
                           cpu: "1"
                   restartPolicy: OnFailure
