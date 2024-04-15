:original_name: cce_10_0775.html

.. _cce_10_0775:

Priority-based Scheduling
=========================

A pod priority indicates the importance of a pod relative to other pods. Volcano supports pod `PriorityClasses <https://kubernetes.io/docs/concepts/scheduling-eviction/pod-priority-preemption/#priorityclass>`__ in Kubernetes. After PriorityClasses are configured, the scheduler preferentially schedules high-priority pods.

Prerequisites
-------------

-  A cluster of v1.19 or later is available. For details, see :ref:`Creating a CCE Cluster <cce_10_0028>`.
-  The Volcano add-on has been installed. For details, see :ref:`Volcano Scheduler <cce_10_0193>`.

Overview
--------

The services running in a cluster are diversified, including core services, non-core services, online services, and offline services. You can configure priorities for different services based on service importance and SLA requirements. For example, configure a high priority for core services and online services so that such services preferentially obtain cluster resources.

:ref:`Table 1 <cce_10_0775__table11718101616464>` lists the priority-based scheduling supported by CCE clusters.

.. _cce_10_0775__table11718101616464:

.. table:: **Table 1** Priority-based scheduling

   +---------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------+
   | Scheduling Type           | Description                                                                                                                                                                                               | Scheduler                           |
   +===========================+===========================================================================================================================================================================================================+=====================================+
   | Priority-based scheduling | The scheduler preferentially guarantees the running of high-priority pods, but will not evict low-priority pods that are running. Priority-based scheduling is enabled by default and cannot be disabled. | kube-scheduler or Volcano scheduler |
   +---------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------+

Procedure
---------

#. Log in to the CCE console.
#. Click the cluster name to access the cluster console. Choose **Settings** in the navigation pane and click the **Scheduling** tab.
#. In the **Business priority scheduling** area, configure priority-based scheduling.

   -  **Scheduling based on priority**: The scheduler preferentially guarantees the running of high-priority pods, but will not evict low-priority pods that are running. Priority-based scheduling is enabled by default and cannot be disabled.

#. After the configuration, you can use `PriorityClasses <https://kubernetes.io/docs/concepts/scheduling-eviction/pod-priority-preemption/#priorityclass>`__ to schedule the pods of workloads or Volcano jobs based priorities.

   a. Create one or more `PriorityClasses <https://kubernetes.io/docs/concepts/scheduling-eviction/pod-priority-preemption/#priorityclass>`__.

      .. code-block::

         apiVersion: scheduling.k8s.io/v1
         kind: PriorityClass
         metadata:
           name: high-priority
         value: 1000000
         globalDefault: false
         description: ""

   b. Create a workload or Volcano job and specify its PriorityClass name.

      -  Workload

         .. code-block::

            apiVersion: apps/v1
            kind: Deployment
            metadata:
              name: high-test
              labels:
                app: high-test
            spec:
              replicas: 5
              selector:
                matchLabels:
                  app: test
              template:
                metadata:
                  labels:
                    app: test
                spec:
                  priorityClassName: high-priority
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

      -  Volcano job

         .. code-block::

            apiVersion: batch.volcano.sh/v1alpha1
            kind: Job
            metadata:
              name: vcjob
            spec:
              schedulerName: volcano
              minAvailable: 4
              priorityClassName: high-priority
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

Example of Priority-based Scheduling
------------------------------------

For example, there are two idle nodes and several workloads with three priorities (high-priority, medium-priority, and low-priority). Run the high-priority workload to exhaust all cluster resources, and issue the medium-priority and low-priority workloads. Then, the two types of workloads are pending due to insufficient resources. When the high-priority workload ends, the pods of the medium-priority workload will be scheduled ahead of the pods of the low-priority workload according to the priority-based scheduling setting.

#. Add three `PriorityClasses <https://kubernetes.io/docs/concepts/scheduling-eviction/pod-priority-preemption/#priorityclass>`__ (**high-priority**, **med-priority**, and **low-priority**) in **priority.yaml**.

   Example configuration of **priority.yaml**:

   .. code-block::

      apiVersion: scheduling.k8s.io/v1
      kind: PriorityClass
      metadata:
        name: high-priority
      value: 100
      globalDefault: false
      description: "This priority class should be used for volcano job only."
      ---
      apiVersion: scheduling.k8s.io/v1
      kind: PriorityClass
      metadata:
        name: med-priority
      value: 50
      globalDefault: false
      description: "This priority class should be used for volcano job only."
      ---
      apiVersion: scheduling.k8s.io/v1
      kind: PriorityClass
      metadata:
        name: low-priority
      value: 10
      globalDefault: false
      description: "This priority class should be used for volcano job only."

   Create PriorityClasses.

   .. code-block::

      kubectl apply -f priority.yaml

#. Check PriorityClasses.

   .. code-block::

      kubectl get PriorityClass

   Command output:

   .. code-block::

      NAME                      VALUE        GLOBAL-DEFAULT   AGE
      high-priority             100          false            97s
      low-priority              10           false            97s
      med-priority              50           false            97s
      system-cluster-critical   2000000000   false            6d6h
      system-node-critical      2000001000   false            6d6h

#. Create a high-priority workload named **high-priority-job** to exhaust all cluster resources.

   high-priority-job.yaml

   .. code-block::

      apiVersion: batch.volcano.sh/v1alpha1
      kind: Job
      metadata:
        name: priority-high
      spec:
        schedulerName: volcano
        minAvailable: 4
        priorityClassName: high-priority
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

   Run the following command to issue the job:

   .. code-block::

      kubectl apply -f high_priority_job.yaml

   Run the **kubectl get pod** command to check pod statuses:

   .. code-block::

      NAME                   READY   STATUS    RESTARTS   AGE
      priority-high-test-0   1/1     Running   0          3s
      priority-high-test-1   1/1     Running   0          3s
      priority-high-test-2   1/1     Running   0          3s
      priority-high-test-3   1/1     Running   0          3s

   The command output shows that all cluster resources have been used up.

#. Create a medium-priority workload **med-priority-job** and a low-priority workload **low-priority-job**.

   med-priority-job.yaml

   .. code-block::

      apiVersion: batch.volcano.sh/v1alpha1
      kind: Job
      metadata:
        name: priority-medium
      spec:
        schedulerName: volcano
        minAvailable: 4
        priorityClassName: med-priority
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

   low-priority-job.yaml

   .. code-block::

      apiVersion: batch.volcano.sh/v1alpha1
      kind: Job
      metadata:
        name: priority-low
      spec:
        schedulerName: volcano
        minAvailable: 4
        priorityClassName: low-priority
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

   Run the following commands to issue the jobs:

   .. code-block::

      kubectl apply -f med_priority_job.yaml
      kubectl apply -f low_priority_job.yaml

   Run the **kubectl get pod** command to check the statuses of the pods for the newly created workloads. The command output shows that the pods are pending due to insufficient resources:

   .. code-block::

      NAME                     READY   STATUS    RESTARTS   AGE
      priority-high-test-0     1/1     Running   0          3m29s
      priority-high-test-1     1/1     Running   0          3m29s
      priority-high-test-2     1/1     Running   0          3m29s
      priority-high-test-3     1/1     Running   0          3m29s
      priority-low-test-0      0/1     Pending   0          2m26s
      priority-low-test-1      0/1     Pending   0          2m26s
      priority-low-test-2      0/1     Pending   0          2m26s
      priority-low-test-3      0/1     Pending   0          2m26s
      priority-medium-test-0   0/1     Pending   0          2m36s
      priority-medium-test-1   0/1     Pending   0          2m36s
      priority-medium-test-2   0/1     Pending   0          2m36s
      priority-medium-test-3   0/1     Pending   0          2m36s

#. Delete the **high_priority_job** workload to release resources and check whether the pods of the **med-priority-job** workload will be preferentially scheduled.

   Run the **kubectl delete -f high_priority_job.yaml** command to release cluster resources and check pod scheduling.

   .. code-block::

      NAME                     READY   STATUS    RESTARTS   AGE
      priority-low-test-0      0/1     Pending   0          5m18s
      priority-low-test-1      0/1     Pending   0          5m18s
      priority-low-test-2      0/1     Pending   0          5m18s
      priority-low-test-3      0/1     Pending   0          5m18s
      priority-medium-test-0   1/1     Running   0          5m28s
      priority-medium-test-1   1/1     Running   0          5m28s
      priority-medium-test-2   1/1     Running   0          5m28s
      priority-medium-test-3   1/1     Running   0          5m28s
