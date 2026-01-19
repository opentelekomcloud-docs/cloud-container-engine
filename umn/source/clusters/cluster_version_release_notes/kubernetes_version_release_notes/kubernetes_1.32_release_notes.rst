:original_name: cce_bulletin_0104.html

.. _cce_bulletin_0104:

Kubernetes 1.32 Release Notes
=============================

CCE now supports Kubernetes 1.32 cluster features. This section describes the changes made in Kubernetes 1.32.

Indexes
-------

-  :ref:`New and Enhanced Features <cce_bulletin_0104__en-us_topic_0000002308456793_section14972102702312>`
-  :ref:`API Changes and Removals <cce_bulletin_0104__en-us_topic_0000002308456793_section1898982110241>`
-  :ref:`Enhanced Kubernetes 1.32 on CCE <cce_bulletin_0104__en-us_topic_0000002308456793_section115291322132513>`
-  :ref:`References <cce_bulletin_0104__en-us_topic_0000002308456793_en-us_topic_0000001072975092_en-us_topic_0261805759_en-us_topic_0261793154_section1272182810583>`

.. _cce_bulletin_0104__en-us_topic_0000002308456793_section14972102702312:

New and Enhanced Features
-------------------------

-  SizeMemoryBackedVolumes (GA)

   SizeMemoryBackedVolumes moved to GA. This feature allows you to specify the size limits for memory-backed volumes (such as emptyDir) to prevent pods from occupying too much node memory. For details, see `emptyDir <https://kubernetes.io/docs/concepts/storage/volumes/#emptydir>`__.

-  PodLifecycleSleepAction (GA)

   PodLifecycleSleepAction moved to GA. This feature allows you to use the **sleep** command by setting the **Sleep** field in the container lifecycle hooks to pause the container for a specified duration. For details, see `Container hooks <https://kubernetes.io/docs/concepts/containers/container-lifecycle-hooks/#container-hooks>`__.

-  StatefulSetAutoDeletePVC (GA)

   StatefulSetAutoDeletePVC moved to GA. This feature allows you to set the PVC retention policy when a StatefulSet is deleted or scaled in, and allows PVCs to be deleted at the same time when a StatefulSet is deleted or scaled in. For details, see `PersistentVolumeClaim retention <https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/#persistentvolumeclaim-retention>`__.

-  CronJobsScheduledAnnotation (GA)

   CronJobsScheduledAnnotation moved to GA. This feature writes the scheduling time of a CronJob to the **batch.kubernetes.io/cronjob-scheduled-timestamp** annotation of a job.

-  PodIndexLabel (GA)

   PodIndexLabel moved to GA. This feature writes the completion sequence of an indexed job to the **batch.kubernetes.io/job-completion-index** label of the pod.

-  CustomResourceFieldSelectors (GA)

   CustomResourceFieldSelectors moved to GA. This feature allows you to specify the **selectableFields** field of a CustomResourceDefinition to define which other fields in a custom resource can be used in field selectors. Field selectors can then be used to get only resources by filtering List, Watch, and DeleteCollection requests. For details, see `Selectable fields for custom resources <https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#crd-selectable-fields>`__.

-  JobManagedBy (Beta)

   JobManagedBy moved to Beta. This feature allows you to specify a custom job controller through the **spec.managedBy** field of the job. For details, see `Delegation of managing a Job object to external controller <https://kubernetes.io/docs/concepts/workloads/controllers/job/#delegation-of-managing-a-job-object-to-external-controller>`__.

-  RelaxedEnvironmentVariableValidation (Beta)

   RelaxedEnvironmentVariableValidation moved to Beta. This feature allows all printable ASCII characters (except =) to be used in environment variable names, increasing the flexibility of environment variable usage. For details, see `Define Environment Variables for a Container <https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/#using-env-var-inside-of-your-config>`__.

-  WatchList (Beta)

   The WatchList feature has been advanced to Beta and is enabled by default. For users who use client-go, after the WatchListClient feature of client-go is enabled, an API client can request a stream of data rather than fetching a full list to access the kube-apiserver, reducing resource consumption on the control plane.

.. _cce_bulletin_0104__en-us_topic_0000002308456793_section1898982110241:

API Changes and Removals
------------------------

-  The metadata annotation **kubernetes.io/enforce-mountable-secrets** of service accounts was deprecated from Kubernetes 1.32, but the removal time was not set. You are advised to use a separate namespace to isolate access to secrets.
-  The kube-controller-manager parameter **concurrent-daemonset-syncs** is available. This parameter controls the number of DaemonSet objects that can synchronize concurrently. This setting can be adjusted using the configuration management function.

.. _cce_bulletin_0104__en-us_topic_0000002308456793_section115291322132513:

Enhanced Kubernetes 1.32 on CCE
-------------------------------

During a version maintenance period, CCE periodically updates Kubernetes 1.32 and provides enhanced functions.

For details about cluster version updates, see :ref:`Patch Versions <cce_10_0405>`.

.. _cce_bulletin_0104__en-us_topic_0000002308456793_en-us_topic_0000001072975092_en-us_topic_0261805759_en-us_topic_0261793154_section1272182810583:

References
----------

For more details about the performance comparison and functional evolution between Kubernetes 1.32 and other versions, see `Kubernetes v1.32 Release Notes <https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.32.md>`__.
