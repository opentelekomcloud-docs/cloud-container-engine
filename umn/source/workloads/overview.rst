:original_name: cce_10_0006.html

.. _cce_10_0006:

Overview
========

CCE provides Kubernetes-native container deployment and management and supports lifecycle management of container workloads, including creation, configuration, monitoring, auto scaling, upgrade, uninstall, service discovery, and load balancing.

Overview of Pod
---------------

A pod is the smallest and simplest unit in the Kubernetes object model that you create or deploy. A pod is a group of one or more containers, with shared storage and network resources, and a specification for how to run the containers. Each pod has a separate IP address.

Pods can be used in either of the following ways:

-  A pod runs only one container. This is the most common usage of pods in Kubernetes. You can consider a pod as a container, but Kubernetes directly manages pods instead of containers.

-  A pod runs multiple containers that need to be tightly coupled. In this scenario, a pod contains a main container and several sidecar containers, as shown in :ref:`Figure 1 <cce_10_0006__en-us_topic_0254767870_fig347141918551>`. For example, the main container is a web server that provides file services from a fixed directory, and sidecar containers periodically download files to this fixed directory.

   .. _cce_10_0006__en-us_topic_0254767870_fig347141918551:

   .. figure:: /_static/images/en-us_image_0258392378.png
      :alt: **Figure 1** Pod running multiple containers

      **Figure 1** Pod running multiple containers

In Kubernetes, pods are rarely created directly. Instead, Kubernetes controller manages pods through pod instances such as Deployments and jobs. A controller typically uses a pod template to create pods. The controller can also manage multiple pods and provide functions such as replica management, rolling upgrade, and self-healing.

Overview of Deployment
----------------------

A pod is the smallest and simplest unit that you create or deploy in Kubernetes. It is designed to be an ephemeral, one-off entity. A pod can be evicted when node resources are insufficient and disappears along with a cluster node failure. Kubernetes provides controllers to manage pods. Controllers can create and manage pods, and provide replica management, rolling upgrade, and self-healing capabilities. The most commonly used controller is Deployment.


.. figure:: /_static/images/en-us_image_0258095884.png
   :alt: **Figure 2** Relationship between a Deployment and pods

   **Figure 2** Relationship between a Deployment and pods

A Deployment can contain one or more pods. These pods have the same role. Therefore, the system automatically distributes requests to multiple pods of a Deployment.

A Deployment integrates a lot of functions, including online deployment, rolling upgrade, replica creation, and restoration of online jobs. To some extent, Deployments can be used to realize unattended rollout, which greatly reduces difficulties and operation risks in the rollout process.

Overview of StatefulSet
-----------------------

All pods under a Deployment have the same characteristics except for the name and IP address. If required, a Deployment can use a pod template to create new pods. If not required, the Deployment can delete any one of the pods.

However, Deployments cannot meet the requirements in some distributed scenarios when each pod requires its own status or in a distributed database where each pod requires independent storage.

Distributed stateful applications involve different roles for different responsibilities. For example, databases work in active/standby mode, and pods depend on each other. To deploy stateful applications in Kubernetes, ensure pods meet the following requirements:

-  Each pod must have a fixed identifier so that it can be recognized by other pods.
-  Separate storage resources must be configured for each pod. In this way, the original data can be retrieved after a pod is deleted and restored. Otherwise, the pod status will be changed after the pod is rebuilt.

To address the preceding requirements, Kubernetes provides StatefulSets.

#. StatefulSets provide a fixed name for each pod following a fixed number ranging from 0 to N. After a pod is rescheduled, the pod name and the hostname remain unchanged.

#. StatefulSets use a headless Service to allocate a fixed domain name for each pod.

#. StatefulSets create PersistentVolumeClaims (PVCs) with fixed identifiers to ensure that pods can access the same persistent data after being rescheduled.

   |image1|

Overview of DaemonSet
---------------------

A DaemonSet runs a pod on each node in a cluster and ensures that there is only one pod. This works well for certain system-level applications such as log collection and resource monitoring since they must run on each node and need only a few pods. A good example is kube-proxy.

DaemonSets are closely related to nodes. If a node becomes faulty, the DaemonSet will not create the same pods on other nodes.


.. figure:: /_static/images/en-us_image_0258871213.png
   :alt: **Figure 3** DaemonSet

   **Figure 3** DaemonSet

Overview of Job and CronJob
---------------------------

Jobs and cron jobs allow you to run short lived, one-off tasks in batch. They ensure the task pods run to completion.

-  A job is a resource object used by Kubernetes to control batch tasks. Jobs are different from long-term servo tasks (such as Deployments and StatefulSets). The former is started and terminated at specific times, while the latter runs unceasingly unless being terminated. The pods managed by a job will be automatically removed after successfully completing tasks based on user configurations.
-  A cron job runs a job periodically on a specified schedule. A cron job object is similar to a line of a crontab file in Linux.

This run-to-completion feature of jobs is especially suitable for one-off tasks, such as continuous integration (CI).

Workload Lifecycle
------------------

.. table:: **Table 1** Status description

   +------------+-------------------------------------------------------------------------------------------------------------------------+
   | Status     | Description                                                                                                             |
   +============+=========================================================================================================================+
   | Running    | All pods are running or the number of pods is 0.                                                                        |
   +------------+-------------------------------------------------------------------------------------------------------------------------+
   | Unready    | The container malfunctions and the pod under the workload is not working.                                               |
   +------------+-------------------------------------------------------------------------------------------------------------------------+
   | Processing | The workload is not running but no error is reported.                                                                   |
   +------------+-------------------------------------------------------------------------------------------------------------------------+
   | Available  | For a multi-pod Deployment, some pods are abnormal but at least one pod is available.                                   |
   +------------+-------------------------------------------------------------------------------------------------------------------------+
   | Completed  | The task is successfully executed. This status is available only for common tasks.                                      |
   +------------+-------------------------------------------------------------------------------------------------------------------------+
   | Stopped    | The workload is stopped and the number of pods changes to 0. This status is available for workloads earlier than v1.13. |
   +------------+-------------------------------------------------------------------------------------------------------------------------+
   | Deleting   | The workload is being deleted.                                                                                          |
   +------------+-------------------------------------------------------------------------------------------------------------------------+

.. |image1| image:: /_static/images/en-us_image_0258203193.png
