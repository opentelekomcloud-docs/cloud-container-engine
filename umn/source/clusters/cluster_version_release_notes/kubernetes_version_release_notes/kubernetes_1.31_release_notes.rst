:original_name: cce_bulletin_0099.html

.. _cce_bulletin_0099:

Kubernetes 1.31 Release Notes
=============================

CCE now supports Kubernetes 1.31 cluster features. This section describes the changes made in Kubernetes 1.31.

Indexes
-------

-  :ref:`New and Enhanced Features <cce_bulletin_0099__en-us_topic_0000002118120745_section14972102702312>`
-  :ref:`API Changes and Removals <cce_bulletin_0099__en-us_topic_0000002118120745_section1898982110241>`
-  :ref:`Enhanced Kubernetes 1.31 on CCE <cce_bulletin_0099__en-us_topic_0000002118120745_section115291322132513>`
-  :ref:`References <cce_bulletin_0099__en-us_topic_0000002118120745_en-us_topic_0000001072975092_en-us_topic_0261805759_en-us_topic_0261793154_section1272182810583>`

.. _cce_bulletin_0099__en-us_topic_0000002118120745_section14972102702312:

New and Enhanced Features
-------------------------

-  Start ordinal of StatefulSets is in the General Availability (GA) state.

   StatefulSet start ordinal is advanced to GA. By default, each pod in a StatefulSet is assigned an integer ordinal from 0. With this feature, you can configure a start ordinal for each pod. For details, see `Start ordinal <https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/#start-ordinal>`__.

-  Elastic indexed jobs are in the GA state.

   Elastic indexed jobs are advanced to GA. You can scale indexed Jobs up or down by modifying **.spec.completions** and **.spec.parallelism**. For details, see `Elastic Indexed Jobs <https://kubernetes.io/docs/concepts/workloads/controllers/job/#elastic-indexed-jobs>`__.

-  Pod failure policy is in the GA state.

   Pod failure policies are advanced to GA. This feature allows you to choose how the system handles pod failures by specifying the processing mode (retry or ignore). This helps prevent unnecessary pod restarts. For details, see `Pod failure policy <https://kubernetes.io/docs/concepts/workloads/controllers/job/#pod-failure-policy>`__.

-  Pod disruption conditions are in the GA state.

   Pod disruption conditions are advanced to GA. The new **DisruptionTarget** condition indicates the reasons for pod failures, such as being preempted by a higher priority pod, cleared due to node deletion, or terminated by kubelet. When a pod is created using a job or CronJob, you can use these pod disruption conditions as part of your job's `pod failure policy <https://kubernetes.io/docs/concepts/workloads/controllers/job/#pod-failure-policy>`__ to define the action when a pod is abnormal. For details, see `Pod disruption conditions <https://kubernetes.io/docs/concepts/workloads/pods/disruptions/#pod-disruption-conditions>`__.

-  Selectable fields for custom resources are in the beta state.

   Selectable fields for custom resources are moved to beta. You can specify the **selectableFields** field of a CustomResourceDefinition (CRD) to define which other fields in a custom resource may be used in field selectors. Field selectors can then be used to get only resources by filtering List, Watch, and DeleteCollection requests. For details, see `Selectable fields for custom resources <https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#crd-selectable-fields>`__.

-  Job success policies are in the beta state.

   Job success policies are moved to beta. This feature allows you to configure success policies for jobs based on the number of pods that succeeded. For details, see `Success policy <https://kubernetes.io/docs/concepts/workloads/controllers/job/#success-policy>`__.

-  **matchLabelKeys** is in the beta state.

   **matchLabelKeys** is moved to beta. **matchLabelKeys** and **mismatchLabelKeys** are finer fields for pod affinity or anti-affinity. They specify the keys for the labels that should or should not match with the incoming pod's labels, so that a rolling upgrade will not break affinity or anti-affinity. For details, see `matchLabelKeys <https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#matchlabelkeys>`__.

-  ServiceAccountTokenNodeBinding is in the beta state.

   ServiceAccountTokenNodeBinding is moved to beta. You can create a service account token that is directly bound to a node. The token defines the node information and verifies whether the node is available. The token will be valid until it expires or either the associated node is deleted. For details, see `Manually create an API token for a ServiceAccount <https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#manually-create-an-api-token-for-a-serviceaccount>`__.

.. _cce_bulletin_0099__en-us_topic_0000002118120745_section1898982110241:

API Changes and Removals
------------------------

-  In Kubernetes 1.31, the **kubectl exec [POD] [COMMAND]** command cannot be executed without a **--** separator. In this case, you need to run **kubectl exec [POD] -- [COMMAND]**.
-  In Kubernetes 1.31, if **caBundle** is not empty but the value is invalid or it does not define any CA certificate, the CRD does not provide services. If **caBundle** is set to a valid value, it remains unchanged if updated. Attempting direct updates results in an "invalid field value" error, ensuring uninterrupted CRD services.

.. _cce_bulletin_0099__en-us_topic_0000002118120745_section115291322132513:

Enhanced Kubernetes 1.31 on CCE
-------------------------------

During a version maintenance period, CCE periodically updates Kubernetes 1.31 and provides enhanced functions.

For details about cluster version updates, see :ref:`Patch Versions <cce_10_0405>`.

.. _cce_bulletin_0099__en-us_topic_0000002118120745_en-us_topic_0000001072975092_en-us_topic_0261805759_en-us_topic_0261793154_section1272182810583:

References
----------

For more details about the performance comparison and function evolution between Kubernetes 1.31 and other versions, see `Kubernetes v1.31 Release Notes <https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.31.md>`__.
