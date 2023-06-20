:original_name: cce_10_0006.html

.. _cce_10_0006:

Overview
========

CCE provides Kubernetes-native container deployment and management and supports lifecycle management of container workloads, including creation, configuration, monitoring, auto scaling, upgrade, uninstall, service discovery, and load balancing.

Pod
---

A pod is the smallest and simplest unit in the Kubernetes object model that you create or deploy. A pod encapsulates one or more containers, storage volumes, a unique network IP address, and options that govern how the containers should run.

Pods can be used in either of the following ways:

-  A container is running in a pod. This is the most common usage of pods in Kubernetes. You can view the pod as a single encapsulated container, but Kubernetes directly manages pods instead of containers.

-  Multiple containers that need to be coupled and share resources run in a pod. In this scenario, an application contains a main container and several sidecar containers, as shown in :ref:`Figure 1 <cce_10_0006__en-us_topic_0254767870_fig347141918551>`. For example, the main container is a web server that provides file services from a fixed directory, and a sidecar container periodically downloads files to the directory.

   .. _cce_10_0006__en-us_topic_0254767870_fig347141918551:

   .. figure:: /_static/images/en-us_image_0000001518222716.png
      :alt: **Figure 1** Pod

      **Figure 1** Pod

In Kubernetes, pods are rarely created directly. Instead, controllers such as Deployments and jobs, are used to manage pods. Controllers can create and manage multiple pods, and provide replica management, rolling upgrade, and self-healing capabilities. A controller generally uses a pod template to create corresponding pods.

Deployment
----------

A pod is the smallest and simplest unit that you create or deploy in Kubernetes. It is designed to be an ephemeral, one-off entity. A pod can be evicted when node resources are insufficient and disappears along with a cluster node failure. Kubernetes provides controllers to manage pods. Controllers can create and manage pods, and provide replica management, rolling upgrade, and self-healing capabilities. The most commonly used controller is Deployment.


.. figure:: /_static/images/en-us_image_0000001569023033.png
   :alt: **Figure 2** Relationship between a Deployment and pods

   **Figure 2** Relationship between a Deployment and pods

A Deployment can contain one or more pods. These pods have the same role. Therefore, the system automatically distributes requests to multiple pods of a Deployment.

A Deployment integrates a lot of functions, including online deployment, rolling upgrade, replica creation, and restoration of online jobs. To some extent, Deployments can be used to realize unattended rollout, which greatly reduces difficulties and operation risks in the rollout process.

StatefulSet
-----------

All pods under a Deployment have the same characteristics except for the name and IP address. If required, a Deployment can use the pod template to create a new pod. If not required, the Deployment can delete any one of the pods.

However, Deployments cannot meet the requirements in some distributed scenarios when each pod requires its own status or in a distributed database where each pod requires independent storage.

With detailed analysis, it is found that each part of distributed stateful applications plays a different role. For example, the database nodes are deployed in active/standby mode, and pods are dependent on each other. In this case, you need to meet the following requirements for the pods:

-  A pod can be recognized by other pods. Therefore, a pod must have a fixed identifier.
-  Each pod has an independent storage device. After a pod is deleted and then restored, the data read from the pod must be the same as the previous one. Otherwise, the pod status is inconsistent.

To address the preceding requirements, Kubernetes provides StatefulSets.

#. A StatefulSet provides a fixed name for each pod following a fixed number ranging from 0 to N. After a pod is rescheduled, the pod name and the host name remain unchanged.

#. A StatefulSet provides a fixed access domain name for each pod through the headless Service (described in following sections).

#. The StatefulSet creates PersistentVolumeClaims (PVCs) with fixed identifiers to ensure that pods can access the same persistent data after being rescheduled.

   |image1|

DaemonSet
---------

A DaemonSet runs a pod on each node in a cluster and ensures that there is only one pod. This works well for certain system-level applications, such as log collection and resource monitoring, since they must run on each node and need only a few pods. A good example is kube-proxy.

DaemonSets are closely related to nodes. If a node becomes faulty, the DaemonSet will not create the same pods on other nodes.


.. figure:: /_static/images/en-us_image_0000001518062772.png
   :alt: **Figure 3** DaemonSet

   **Figure 3** DaemonSet

Job and Cron Job
----------------

Jobs and cron jobs allow you to run short lived, one-off tasks in batch. They ensure the task pods run to completion.

-  A job is a resource object used by Kubernetes to control batch tasks. Jobs are different from long-term servo tasks (such as Deployments and StatefulSets). The former is started and terminated at specific times, while the latter runs unceasingly unless being terminated. The pods managed by a job will be automatically removed after successfully completing tasks based on user configurations.
-  A cron job runs a job periodically on a specified schedule. A cron job object is similar to a line of a crontab file in Linux.

This run-to-completion feature of jobs is especially suitable for one-off tasks, such as continuous integration (CI).

Workload Lifecycle
------------------

.. table:: **Table 1** Status description

   +------------------------+-------------------------------------------------------------------------------------------------------------------------+
   | Status                 | Description                                                                                                             |
   +========================+=========================================================================================================================+
   | Running                | All pods are running.                                                                                                   |
   +------------------------+-------------------------------------------------------------------------------------------------------------------------+
   | Unready                | A container is abnormal, the number of pods is 0, or the workload is in pending state.                                  |
   +------------------------+-------------------------------------------------------------------------------------------------------------------------+
   | Upgrading/Rolling back | The workload is being upgraded or rolled back.                                                                          |
   +------------------------+-------------------------------------------------------------------------------------------------------------------------+
   | Available              | For a multi-pod Deployment, some pods are abnormal but at least one pod is available.                                   |
   +------------------------+-------------------------------------------------------------------------------------------------------------------------+
   | Completed              | The task is successfully executed. This status is available only for common tasks.                                      |
   +------------------------+-------------------------------------------------------------------------------------------------------------------------+
   | Stopped                | The workload is stopped and the number of pods changes to 0. This status is available for workloads earlier than v1.13. |
   +------------------------+-------------------------------------------------------------------------------------------------------------------------+
   | Deleting               | The workload is being deleted.                                                                                          |
   +------------------------+-------------------------------------------------------------------------------------------------------------------------+
   | Pausing                | The workload is being paused.                                                                                           |
   +------------------------+-------------------------------------------------------------------------------------------------------------------------+

.. |image1| image:: /_static/images/en-us_image_0000001517743628.png
