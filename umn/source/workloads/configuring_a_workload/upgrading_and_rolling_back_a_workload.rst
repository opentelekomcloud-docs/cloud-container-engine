:original_name: cce_10_0397.html

.. _cce_10_0397:

Upgrading and Rolling Back a Workload
=====================================

After a workload is created, you can upgrade it and roll it back. The flexible upgrade and rollback mechanism enables smooth version transitions without interrupting services. If an issue arises during an upgrade, you can quickly restore to the previous stable state. The upgrade and rollback mechanism applies to multiple workloads:

-  Upgrade: applies to Deployments, StatefulSets, and DaemonSets.
-  Rollback: applies to Deployments.

Using the Console
-----------------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Workloads**. In the upper right corner of the displayed page, click **Create Workload**.

#. In the **Upgrade** area under **Advanced Settings**, select an upgrade mode. For details, see :ref:`Table 1 <cce_10_0397__table1485533151215>`. The common parameters for rolling upgrades and replacement upgrades serve similar purposes, though their implementations differ.

   .. _cce_10_0397__table1485533151215:

   .. table:: **Table 1** Workload upgrade modes

      +------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------+
      | Parameter                                            | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | Constraint                                                       |
      +======================================================+==============================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================+==================================================================+
      | Max. Unavailable Pods (maxUnavailable)               | The maximum number or percentage of pods that can be unavailable during a rolling upgrade. This also sets the limit for how many running pods can be below the expected number. The default value is **25%**. During an upgrade, the percentage is converted into an absolute number and **rounded down**.                                                                                                                                                                                                                   | This parameter is only available for Deployments and DaemonSets. |
      |                                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |                                                                  |
      |                                                      | For example, if **spec.replicas** is set to **2**, no pods (2 x 0.25 = 0.5, rounded down to 0) can be unavailable. Therefore, during an upgrade, there will always be at least two pods running (2 desired - 0 unavailable). Each old pod is deleted only after a new one is created, ensuring that at least two pods are always running until all pods are updated.                                                                                                                                                         |                                                                  |
      +------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------+
      | Max. Surge (maxSurge)                                | The maximum number or percentage of pods that can exist above the desired number of pods during a rolling upgrade. This parameter determines the maximum number of new pods that can be created at a time to replace old pods. The default value is **25%**. During an upgrade, the percentage is converted into an absolute number and **rounded up**.                                                                                                                                                                      | This parameter is only available for Deployments and DaemonSets. |
      |                                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |                                                                  |
      |                                                      | For example, if **spec.replicas** is set to **2**, a maximum of one pod (2 x 0.25 = 0.5, rounded up to 1) can be created at a time by default. Therefore, during an upgrade, up to 3 pods can exist (2 desired + 1 surge).                                                                                                                                                                                                                                                                                                   |                                                                  |
      +------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------+
      | Min. Ready Seconds (minReadySeconds)                 | The minimum duration a new pod must remain in the ready state before it is marked as available.                                                                                                                                                                                                                                                                                                                                                                                                                              | None                                                             |
      |                                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |                                                                  |
      |                                                      | This ensures a stable observation period, preventing unstable pods from joining the service too early and thereby improving the stability and reliability of the deployment.                                                                                                                                                                                                                                                                                                                                                 |                                                                  |
      +------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------+
      | Revision History Limit (revisionHistoryLimit)        | The maximum number of old ReplicaSets retained for rollback. These consume etcd resources and appear in **kubectl get rs** output.                                                                                                                                                                                                                                                                                                                                                                                           | None                                                             |
      |                                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |                                                                  |
      |                                                      | Each Deployment's historical configurations are stored in its corresponding ReplicaSet. Deleting an old ReplicaSet makes it impossible to roll back the Deployment to that version. By default, CCE retains the latest 10 old ReplicaSets.                                                                                                                                                                                                                                                                                   |                                                                  |
      +------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------+
      | Max. Upgrade Duration (progressDeadlineSeconds)      | The maximum time, in seconds, that a Deployment can take to make progress before it is considered to be failing. If specifying this parameter, ensure its value is greater than **minReadySeconds**.                                                                                                                                                                                                                                                                                                                         | This parameter is only available for Deployments.                |
      |                                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |                                                                  |
      |                                                      | During a rolling upgrade, if a Deployment does not make progress within the time specified by **progressDeadlineSeconds**, it is marked with the following conditions: **Type=Progressing**, **Status=False**, and **Reason=ProgressDeadlineExceeded**. The above information indicates that the Deployment upgrade has failed. CCE records the failure event but does not automatically roll back the Deployment to a previous version. The rollback must be manually performed or triggered through an external mechanism. |                                                                  |
      +------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------+
      | Scale-In Time Window (terminationGracePeriodSeconds) | The maximum time kubelet waits for containers in a pod to exit gracefully when the pod is deleted. The default is 30 seconds. Containers should exit within this period. Otherwise, they will be forcibly terminated with SIGKILL.                                                                                                                                                                                                                                                                                           | None                                                             |
      +------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------+

#. Configure other parameters and click **Create Workload**. The workload status will change to **Running** later.

Using kubectl
-------------

The following uses a Deployment as an example to describe how to configure a workload upgrade policy using kubectl.

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Create a file named **nginx-deployment.yaml**. The name is only an example. You can rename it as needed.

   .. code-block::

      vi nginx-deployment.yaml

   Below is an example of the file. For details about the Deployment configuration, see the `Kubernetes official documentation <https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/deployment-v1/>`__.

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: nginx
        namespace: default
      spec:
        replicas: 2
        selector:
          matchLabels:
            app: nginx
        template:
          metadata:
            labels:
              app: nginx
          spec:
            containers:
            - image: nginx:latest
              imagePullPolicy: Always
              name: nginx
              resources:
                requests:
                  cpu: 250m
                  memory: 512Mi
                limits:
                  cpu: 250m
                  memory: 512Mi
            imagePullSecrets:
            - name: default-secret
            terminationGracePeriodSeconds: 30
        strategy:
          type: RollingUpdate # A rolling upgrade. Recreate indicates a replacement upgrade.
          rollingUpdate: # Configure rolling upgrade parameters.
            maxUnavailable: 25%
            maxSurge: 25%
        minReadySeconds: 0
        revisionHistoryLimit: 10
        progressDeadlineSeconds: 600

   .. table:: **Table 2** Workload upgrade modes

      +-------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------+
      | Parameter                     | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  | Constraint                                             |
      +===============================+==============================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================+========================================================+
      | maxUnavailable                | The maximum number or percentage of pods that can be unavailable during a rolling upgrade. This also sets the limit for how many running pods can be below the expected number. The default value is **25%**. During an upgrade, the percentage is converted into an absolute number and **rounded down**.                                                                                                                                                                                                                   | This parameter is only available for rolling upgrades. |
      |                               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |                                                        |
      |                               | For example, if **spec.replicas** is set to **2**, no pods (2 x 0.25 = 0.5, rounded down to 0) can be unavailable. Therefore, during an upgrade, there will always be at least two pods running (2 desired - 0 unavailable). Each old pod is deleted only after a new one is created, ensuring that at least two pods are always running until all pods are updated.                                                                                                                                                         |                                                        |
      +-------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------+
      | maxSurge                      | The maximum number or percentage of pods that can exist above the desired number of pods during a rolling upgrade. This parameter determines the maximum number of new pods that can be created at a time to replace old pods. The default value is **25%**. During an upgrade, the percentage is converted into an absolute number and **rounded up**.                                                                                                                                                                      | This parameter is only available for rolling upgrades. |
      |                               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |                                                        |
      |                               | For example, if **spec.replicas** is set to **2**, a maximum of one pod (2 x 0.25 = 0.5, rounded up to 1) can be created at a time by default. Therefore, during an upgrade, up to 3 pods can exist (2 desired + 1 surge).                                                                                                                                                                                                                                                                                                   |                                                        |
      +-------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------+
      | minReadySeconds               | The minimum duration a new pod must remain in the ready state before it is marked as available.                                                                                                                                                                                                                                                                                                                                                                                                                              | None                                                   |
      |                               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |                                                        |
      |                               | This ensures a stable observation period, preventing unstable pods from joining the service too early and thereby improving the stability and reliability of the deployment.                                                                                                                                                                                                                                                                                                                                                 |                                                        |
      +-------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------+
      | revisionHistoryLimit          | The maximum number of old ReplicaSets retained for rollback. These consume etcd resources and appear in **kubectl get rs** output.                                                                                                                                                                                                                                                                                                                                                                                           | None                                                   |
      |                               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |                                                        |
      |                               | Each Deployment's historical configurations are stored in its corresponding ReplicaSet. Deleting an old ReplicaSet makes it impossible to roll back the Deployment to that version. By default, CCE retains the latest 10 old ReplicaSets.                                                                                                                                                                                                                                                                                   |                                                        |
      +-------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------+
      | progressDeadlineSeconds       | The maximum time, in seconds, that a Deployment can take to make progress before it is considered to be failing. If specifying this parameter, ensure its value is greater than **minReadySeconds**.                                                                                                                                                                                                                                                                                                                         | None                                                   |
      |                               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |                                                        |
      |                               | During a rolling upgrade, if a Deployment does not make progress within the time specified by **progressDeadlineSeconds**, it is marked with the following conditions: **Type=Progressing**, **Status=False**, and **Reason=ProgressDeadlineExceeded**. The above information indicates that the Deployment upgrade has failed. CCE records the failure event but does not automatically roll back the Deployment to a previous version. The rollback must be manually performed or triggered through an external mechanism. |                                                        |
      +-------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------+
      | terminationGracePeriodSeconds | The maximum time kubelet waits for containers in a pod to exit gracefully when the pod is deleted. The default is 30 seconds. Containers should exit within this period. Otherwise, they will be forcibly terminated with SIGKILL.                                                                                                                                                                                                                                                                                           | None                                                   |
      +-------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------+

#. Create the Deployment.

   .. code-block::

      kubectl create -f nginx-deployment.yaml

   If information similar to the following is displayed, the Deployment is being created:

   .. code-block::

      deployment.apps/nginx created

#. Check the Deployment status.

   .. code-block::

      kubectl get deployment

   If information similar to the following is displayed, the Deployment has been created:

   .. code-block::

      NAME           READY     UP-TO-DATE   AVAILABLE   AGE
      nginx          2/2       2            2           4m5s

#. Change the image used by the Deployment to **nginx:alpine**.

   .. code-block::

      kubectl edit deploy nginx

   Modify the image in **spec.containers.image**, save the modification, and exit.

#. Check the ReplicaSets of the Deployment.

   .. code-block::

      kubectl get rs

   Information similar to the following is displayed:

   .. code-block::

      NAME               DESIRED   CURRENT   READY     AGE
      nginx-6f9f58dffd   2         2         2         1m    # New-version ReplicaSet (activated)
      nginx-7f98958cdf   0         0         0         48m   # Old-version ReplicaSet (deactivated)

   During the upgrade, with **spec.replicas** set to **2** and both **maxSurge** and **maxUnavailable** set to **25%**, the number of pods changes as follows:

   -  **maxSurge** = **1** (2 x 25% = 0.5, rounded up) -> One extra pod can be added at most.
   -  **maxUnavailable** = **0** (2 x 25% = 0.5, rounded down) -> No unavailable pod is allowed.

   During the upgrade, up to three pods can exist in total, with two always available for services. CCE creates new pods, checks their readiness, and deletes old pods one by one until all are upgraded.

#. After the upgrade is complete, check the pod statuses:

   .. code-block::

      kubectl get pods

   In the command output, if all the pods are **Running**, the Deployment has been upgraded.

   .. code-block::

      NAME                     READY     STATUS    RESTARTS   AGE
      nginx-6f9f58dffd-tdmqk   1/1       Running   0          1m
      nginx-6f9f58dffd-tesqr   1/1       Running   0          1m

Rolling Back a Workload
-----------------------

If an error occurs during an upgrade, you can roll back the Deployment to its previous version. This is possible because old-version ReplicaSets are retained after each upgrade. A rollback essentially replaces the upgraded ReplicaSet with an old one. You can use **revisionHistoryLimit** to control the maximum number of retained historical versions (default: 10).

-  **Using the console**

   #. Log in to the CCE console and click the cluster name to access the cluster console.
   #. In the navigation pane, choose **Workloads**. In the **Operation** column of the target workload, choose **More** > **Roll Back**.

-  **Using kubectl**

   Run the following command to roll back the target Deployment:

   .. code-block::

      kubectl rollout undo deployment nginx

   If information similar to the following is displayed, the rollback is successful:

   .. code-block::

      deployment.apps/nginx rolled back
