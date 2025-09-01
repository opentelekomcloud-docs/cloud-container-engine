:original_name: cce_10_0006.html

.. _cce_10_0006:

Overview
========

A workload is an application running on Kubernetes. No matter how many components are there in your workload, you can run it in a group of Kubernetes pods. A workload is an abstract model of a group of pods in Kubernetes. Workloads in Kubernetes are classified as Deployments, StatefulSets, DaemonSets, jobs, and cron jobs.

CCE provides Kubernetes-native container deployment and management and supports lifecycle management of container workloads, including creation, configuration, monitoring, auto scaling, upgrade, uninstall, service discovery, and load balancing.

Overview of Pods
----------------

Pods are the smallest unit that you can create or deploy in Kubernetes. Each pod comprises one or more containers, shared storage (volumes), a unique IP address, and container runtime policies.

Pods can be used in either of the following ways:

-  A pod runs a single container. This is the most common scenario in Kubernetes. In this case, a pod can be thought of as a container, although Kubernetes manages the pod rather than the container itself.

-  A pod runs multiple tightly coupled containers that need to share resources. In this case, the pod includes a main container and several sidecar containers, as shown in :ref:`Figure 1 <cce_10_0006__en-us_topic_0254767870_fig347141918551>`. For example, the main container might be a web server providing file services from a fixed directory, while sidecar containers periodically download files to that directory.

   .. _cce_10_0006__en-us_topic_0254767870_fig347141918551:

   .. figure:: /_static/images/en-us_image_0258392378.png
      :alt: **Figure 1** A pod running multiple containers

      **Figure 1** A pod running multiple containers

In Kubernetes, you rarely create pods directly. Instead, controllers like Deployments and jobs create and manage them. These controllers typically use pod templates to create and manage pods, providing features like replica management, rolling upgrades, and self-healing.

Overview of Deployments
-----------------------

A pod is the smallest unit that you create or deploy in Kubernetes. It is evicted when resources are tight and gone if its node fails. Kubernetes provides controllers to manage pods. These controllers create and manage pods, providing features like replica management, rolling upgrades, and self-healing. The most common controller is Deployment.


.. figure:: /_static/images/en-us_image_0258095884.png
   :alt: **Figure 2** Relationship between a Deployment and pods

   **Figure 2** Relationship between a Deployment and pods

A Deployment runs one or more identical pods. Kubernetes load-balances traffic across them.

A Deployment handles rollout, rolling upgrades, scaling, and automatic replacement of failed pods. This enables zero-touch releases with minimal risk.

Overview of StatefulSets
------------------------

All pods under a Deployment are identical except for their names and IP addresses. Deployments can create new pods using a pod template and delete any pod when not needed.

However, Deployments are not suitable for distributed scenarios where each pod requires its own status or independent storage, such as in distributed databases.

Distributed stateful applications often involve different roles and responsibilities. For example, databases may operate in active/standby mode, and pods may depend on each other. To deploy stateful applications in Kubernetes, pods must meet the following requirements:

-  Each pod must have a unique, fixed identifier to be recognized by other pods.
-  Each pod should be configured with separate storage resources to ensure data persistence. This allows the original data to be retained and retrieved even after a pod is deleted and recreated. Without dedicated storage, the pod's data will be lost upon deletion, and the new pod will initialize with a different state.

To address these requirements, Kubernetes provides StatefulSets:

#. StatefulSets provide a fixed name for each pod, followed by a sequential numeric suffix (for example, pod-0, pod-1, ..., pod-N). After a pod is rescheduled, its name and hostname remain unchanged.

#. StatefulSets use a headless Service to allocate a fixed domain name for each pod.

#. StatefulSets create PVCs with fixed identifiers. This ensures that pods can access the original persistent data after being rescheduled.


   .. figure:: /_static/images/en-us_image_0258203193.png
      :alt: **Figure 3** StatefulSet

      **Figure 3** StatefulSet

Overview of DaemonSet
---------------------

A DaemonSet runs a pod on each node in a cluster and ensures that there is only one pod. This works well for certain system-level applications such as log collection and resource monitoring since they must run on each node. A good example is kube-proxy.

DaemonSets are closely related to nodes. If a node becomes faulty, the DaemonSet will not create the same pods on other nodes.


.. figure:: /_static/images/en-us_image_0258871213.png
   :alt: **Figure 4** DaemonSet

   **Figure 4** DaemonSet

Overview of Jobs and CronJobs
-----------------------------

Jobs and CronJobs are Kubernetes resources designed to manage short-lived, one-off tasks that run to completion.

-  A job is a resource object used to control batch tasks. Jobs start and terminate at specific times, unlike long-running services such as Deployments and StatefulSets, which run continuously unless terminated. Pods managed by a job are automatically removed after successfully completing their tasks, based on the specified settings.
-  A CronJob runs a job periodically on a specified schedule. A CronJob object is similar to a line in a crontab file in Linux.

The run-to-completion feature of workloads makes them particularly suitable for one-off tasks, such as continuous integration (CI) pipelines.

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
