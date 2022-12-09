:original_name: cce_01_0013.html

.. _cce_01_0013:

Managing Pods
=============

Scenario
--------

A pod is the smallest and simplest unit in the Kubernetes object model that you create or deploy. A pod encapsulates an application's container (or, in some cases, multiple containers), storage resources, a unique network identity (IP address), as well as options that govern how the container(s) should run. A pod represents a single instance of an application in Kubernetes, which might consist of either a single container or a small number of containers that are tightly coupled and that share resources.

Pods in a Kubernetes cluster can be used in either of the following ways:

-  **Pods that run a single container.** The "one-container-per-pod" model is the most common Kubernetes use case. In this case, a pod functions as a wrapper around a single container, and Kubernetes manages the pods rather than the containers directly.
-  **Pods that run multiple containers that need to work together.** A pod might encapsulate an application composed of multiple co-located containers that are tightly coupled and need to share resources. The possible scenarios are as follows:

   -  Content management systems, file and data loaders, local cache managers, etc;
   -  Log and checkpoint backup, compression, rotation, snapshotting, etc;
   -  Data change watchers, log tailers, logging and monitoring adapters, event publishers, etc;
   -  Proxies, bridges, adapters, etc;
   -  Controllers, managers, configurators, and updaters

You can easily manage pods on CCE, such as editing YAML files and monitoring pods.

Editing a YAML File
-------------------

To edit and download the YAML file of a pod online, do as follows:

#. Log in to the CCE console. In the navigation pane, choose **Workloads** > **Pods**.
#. Click **Edit YAML** at the same row as the target pod. In the **Edit YAML** dialog box displayed, modify the YAML file of the pod.
#. Click **Edit** and then **OK** to save the changes.

   .. note::

      If a pod is created by another workload, its YAML file cannot be modified individually on the **Pods** page.

#. (Optional) In the **Edit YAML** window, click **Download** to download the YAML file.

Monitoring Pods
---------------

On the CCE console, you can view the CPU and memory usage, upstream and downstream rates, and disk read/write rates of a workload pod to determine the required resource specifications.

#. Log in to the CCE console. In the navigation pane, choose **Workloads** > **Pods**.
#. Click **Monitoring** at the same row as the target pod to view the CPU and memory usage, upstream and downstream rates, and disk read/write rates of the pod.

   .. note::

      You cannot view the monitoring data of a pod that is not running.

Deleting a Pod
--------------

If a pod is no longer needed, you can delete it. Deleted pods cannot be recovered. Exercise caution when performing this operation.

#. Log in to the CCE console. In the navigation pane, choose **Workloads** > **Pods**.

#. Click **Delete** at the same row as the pod to be deleted.

   Read the system prompts carefully. A pod cannot be restored after it is deleted. Exercise caution when performing this operation.

#. Click **Yes** to delete the pod.

   .. note::

      -  If the node where the pod is located is unavailable or shut down and the workload cannot be deleted, you can forcibly delete the pod from the pod list on the workload details page.
      -  Ensure that the storage volumes to be deleted are not used by other workloads. If these volumes are imported or have snapshots, you can only unbind them.

Helpful Links
-------------

-  `The Distributed System Toolkit: Patterns for Composite Containers <https://kubernetes.io/blog/2015/06/the-distributed-system-toolkit-patterns>`__
-  `Container Design Patterns <https://kubernetes.io/blog/2016/06/container-design-patterns/>`__
