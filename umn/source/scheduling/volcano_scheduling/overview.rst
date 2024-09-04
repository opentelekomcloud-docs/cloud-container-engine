:original_name: cce_10_0721.html

.. _cce_10_0721:

Overview
========

Volcano is a Kubernetes-based batch processing platform that supports machine learning, deep learning, bioinformatics, genomics, and other big data applications. It provides general-purpose, high-performance computing capabilities, such as job scheduling, heterogeneous chip management, and job running management.

Volcano Scheduler
-----------------

Volcano Scheduler is a pod scheduling component, which consists of a series of actions and plugins. Actions should be executed in every step. Plugins provide the action algorithm details in different scenarios. Volcano Scheduler features high scalability. You can specify actions and plugins as needed.


.. figure:: /_static/images/en-us_image_0000001950316688.png
   :alt: **Figure 1** Volcano Scheduler workflow

   **Figure 1** Volcano Scheduler workflow

The working process of Volcano Scheduler is as follows:

#. Identify and cache the job submitted by the client.
#. Start a periodical session. A scheduling cycle begins.
#. Send jobs that are not scheduled the to-be-scheduled queue of the session.
#. Traverse all jobs to be scheduled and perform actions such as enqueue, allocate, preempt, reclaim, and backfill in the configured sequence to find the most appropriate node for each job. Bind the job to the node. The specific algorithm logic executed in **action** depends on the implementation of each function in the registered plugin.
#. Close the session.

Custom Volcano Resources
------------------------

-  A pod group is a custom Volcano resource type. It is a group of pods with strong association and is mainly used in batch scheduling, for example, ps and worker tasks in TensorFlow.
-  A Queue contains a group of PodGroups. It is also the basis for the PodGroups to obtain cluster resources.
-  Volcano Job (vcjob for short) is a custom job resource type. Different from Kubernetes Jobs, vcjob supports specified scheduler, the minimum number of running pods, tasks, lifecycle management, specified queues, and priority-based scheduling. Volcano Job is more suitable for high-performance computing scenarios such as machine learning, big data, and scientific computing.
