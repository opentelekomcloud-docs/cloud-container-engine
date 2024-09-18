:original_name: cce_10_0397.html

.. _cce_10_0397:

Configuring Workload Upgrade Policies
=====================================

In actual applications, upgrade is a common operation. A Deployment, StatefulSet, or DaemonSet can easily support application upgrade.

You can set different upgrade policies:

-  **Rolling upgrade**: New pods are created gradually and then old pods are deleted. This is the default policy.
-  **Replace upgrade**: The current pods are deleted and then new pods are created.

Upgrade Parameters
------------------

+------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------+
| Parameter                                            | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Constraint                                                      |
+======================================================+=======================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================+=================================================================+
| Max. Surge (maxSurge)                                | Specifies the maximum number of pods that can exist compared with **spec.replicas**. The default value is **25%**.                                                                                                                                                                                                                                                                                                                                                                                    | This parameter is supported only by Deployments and DaemonSets. |
|                                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |                                                                 |
|                                                      | For example, if **spec.replicas** is set to **4**, a maximum of five pods can exist during the upgrade. That is, the upgrade is performed at a step of 1. During the actual upgrade, the value is converted into a number and rounded up. The value can also be set to an absolute number.                                                                                                                                                                                                            |                                                                 |
+------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------+
| Max. Unavailable Pods (maxUnavailable)               | Specifies the maximum number of pods that can be unavailable compared with **spec.replicas**. The default value is **25%**.                                                                                                                                                                                                                                                                                                                                                                           | This parameter is supported only by Deployments and DaemonSets. |
|                                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |                                                                 |
|                                                      | For example, if **spec.replicas** is set to **4**, at least three pods exist during the upgrade. That is, the deletion is performed at a step of 1. The value can also be set to an absolute number.                                                                                                                                                                                                                                                                                                  |                                                                 |
+------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------+
| **Min. Ready Seconds** (minReadySeconds)             | A pod is considered available only when the minimum readiness time is exceeded without any of its containers crashing. The default value is **0** (the pod is considered available immediately after it is ready).                                                                                                                                                                                                                                                                                    | None                                                            |
+------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------+
| Revision History Limit (revisionHistoryLimit)        | Specifies the number of old ReplicaSets to retain to allow rollback. These old ReplicaSets consume resources in etcd and crowd the output of **kubectl get rs**. The configuration of each Deployment revision is stored in its ReplicaSets. Therefore, once the old ReplicaSet is deleted, you lose the ability to roll back to that revision of Deployment. By default, 10 old ReplicaSets will be kept, but the ideal value depends on the frequency and stability of the new Deployments.         | None                                                            |
+------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------+
| Max. Upgrade Duration (progressDeadlineSeconds)      | Specifies the number of seconds that the system waits for a Deployment to make progress before reporting a Deployment progress failure. It is surfaced as a condition with Type=Progressing, Status=False, and Reason=ProgressDeadlineExceeded in the status of the resource. The Deployment controller will keep retrying the Deployment. In the future, once automatic rollback will be implemented, the Deployment controller will roll back a Deployment as soon as it observes such a condition. | None                                                            |
|                                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |                                                                 |
|                                                      | If this parameter is specified, the value of this parameter must be greater than that of **.spec.minReadySeconds**.                                                                                                                                                                                                                                                                                                                                                                                   |                                                                 |
+------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------+
| Scale-In Time Window (terminationGracePeriodSeconds) | Graceful deletion time. The default value is 30 seconds. When a pod is deleted, a SIGTERM signal is sent and the system waits for the applications in the container to terminate. If the application is not terminated within the time specified by **terminationGracePeriodSeconds**, a SIGKILL signal is sent to forcibly terminate the pod.                                                                                                                                                        | None                                                            |
+------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------+

Upgrade Example
---------------

The Deployment can be upgraded in a declarative mode. That is, you only need to modify the YAML definition of the Deployment. For example, you can run the **kubectl edit** command to change the Deployment image to **nginx:alpine**. After the modification, query the ReplicaSet and pod. The query result shows that a new ReplicaSet is created and the pod is re-created.

.. code-block::

   $ kubectl edit deploy nginx

   $ kubectl get rs
   NAME               DESIRED   CURRENT   READY     AGE
   nginx-6f9f58dffd   2         2         2         1m
   nginx-7f98958cdf   0         0         0         48m

   $ kubectl get pods
   NAME                     READY     STATUS    RESTARTS   AGE
   nginx-6f9f58dffd-tdmqk   1/1       Running   0          1m
   nginx-6f9f58dffd-tesqr   1/1       Running   0          1m

The Deployment can use the **maxSurge** and **maxUnavailable** parameters to control the proportion of pods to be re-created during the upgrade, which is useful in many scenarios. The configuration is as follows:

.. code-block::

   spec:
     strategy:
       rollingUpdate:
         maxSurge: 1
         maxUnavailable: 0
       type: RollingUpdate

In the preceding example, the value of **spec.replicas** is **2**. If both **maxSurge** and **maxUnavailable** are the default value 25%, **maxSurge** allows a maximum of three pods to exist (2 x 1.25 = 2.5, rounded up to 3), and **maxUnavailable** does not allow a maximum of two pods to be unavailable (2 x 0.75 = 1.5, rounded up to 2). That is, during the upgrade process, there will always be two pods running. Each time a new pod is created, an old pod is deleted, until all pods are new.

Rollback
--------

Rollback is to roll an application back to an earlier version when a fault occurs during an upgrade. A Deployment can be easily rolled back to the earlier version.

For example, if the upgraded image is faulty, you can run the **kubectl rollout undo** command to roll back the Deployment.

.. code-block::

   $ kubectl rollout undo deployment nginx
   deployment.apps/nginx rolled back

A Deployment can be easily rolled back because it uses a ReplicaSet to control a pod. After the upgrade, the previous ReplicaSet still exists. The Deployment is rolled back by using the previous ReplicaSet to re-create the pod. The number of ReplicaSets stored in a Deployment can be restricted by the **revisionHistoryLimit** parameter. The default value is **10**.
