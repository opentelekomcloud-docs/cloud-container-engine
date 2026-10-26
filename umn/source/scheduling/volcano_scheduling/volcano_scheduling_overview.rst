:original_name: cce_10_0721.html

.. _cce_10_0721:

Volcano Scheduling Overview
===========================

`Volcano <https://volcano.sh/>`__ is a Kubernetes-native batch processing platform for machine learning, deep learning, bioinformatics, genomics, and other big data applications. It provides general-purpose, high-performance computing capabilities, such as job scheduling, heterogeneous chip management, and job running management.

Volcano Scheduler
-----------------

Volcano Scheduler is a pod scheduling component, which consists of a series of actions and plugins. Actions should be executed in every step. Plugins provide the action algorithm details in different scenarios. Volcano Scheduler is highly scalable. You can specify actions and plugins as needed.


.. figure:: /_static/images/en-us_image_0000002631530474.png
   :alt: **Figure 1** Volcano Scheduler workflow

   **Figure 1** Volcano Scheduler workflow

The working process of Volcano Scheduler is as follows:

#. The scheduler identifies and caches the jobs (vcjobs) submitted by the client. All pods belonging to the same job are grouped into a PodGroup.
#. When a PodGroup meets the scheduling requirements, it is added to the Volcano scheduling queue. The scheduler periodically starts sessions to process scheduling.
#. A predefined sequence of actions, such as enqueue, allocate, preempt, reclaim, and backfill, is executed to determine the most suitable node for each pod and schedule the pod accordingly. The specific algorithm used in each action depends on the implementation of the corresponding plugin functions such as DRF, priority, and Gang.
#. The scheduler iterates through all pending jobs until every job has been processed, completing the scheduling cycle.

Custom Volcano Resources
------------------------

-  A pod group is a custom Volcano resource type. It is a group of pods with strong association and is mainly used in batch scheduling, for example, ps and worker tasks in TensorFlow.
-  A queue contains a group of PodGroups. It is also the basis for the PodGroups to obtain cluster resources.
-  Volcano Job (vcjob for short) is a custom job resource type. Different from Kubernetes Jobs, vcjob supports specified scheduler, the minimum number of running pods, tasks, lifecycle management, specified queues, and priority-based scheduling. Volcano Job is more suitable for high-performance computing scenarios such as machine learning, big data, and scientific computing.
