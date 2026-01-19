:original_name: cce_bulletin_0105.html

.. _cce_bulletin_0105:

Kubernetes 1.33 Release Notes
=============================

CCE has passed the Certified Kubernetes Conformance Program and is a certified Kubernetes offering. CCE now supports Kubernetes 1.33 cluster features. This section describes the changes made in Kubernetes 1.33.

Indexes
-------

-  :ref:`New and Enhanced Features <cce_bulletin_0105__en-us_topic_0000002447366897_section14972102702312>`
-  :ref:`API Changes and Removals <cce_bulletin_0105__en-us_topic_0000002447366897_section1898982110241>`
-  :ref:`Enhanced Kubernetes 1.33 on CCE <cce_bulletin_0105__en-us_topic_0000002447366897_section115291322132513>`
-  :ref:`References <cce_bulletin_0105__en-us_topic_0000002447366897_en-us_topic_0000001072975092_en-us_topic_0261805759_en-us_topic_0261793154_section1272182810583>`

.. _cce_bulletin_0105__en-us_topic_0000002447366897_section14972102702312:

New and Enhanced Features
-------------------------

-  CPUManagerPolicyOptions (GA)

   CPUManagerPolicyOptions moved to GA. It allows you to fine-tune the resource allocation policies of CPU manager. For details, see `Control CPU Management Policies on the Node <https://kubernetes.io/docs/tasks/administer-cluster/cpu-management-policies/#cpu-policy-static--options>`__.

-  MultiCIDRServiceAllocator (GA)

   MultiCIDRServiceAllocator moved to GA. ServiceCIDRs and IPAddresses are introduced to record the allocations of Service cluster IP addresses. You can use ServiceCIDRs to increase the number of available cluster IP addresses.

-  JobBackoffLimitPerIndex (GA)

   JobBackoffLimitPerIndex moved to GA. It allows you to control the maximum number of retries per index in an indexed job. For details, see `Kubernetes v1.33: Job's Backoff Limit Per Index Goes GA <https://kubernetes.io/blog/2025/05/13/kubernetes-v1-33-jobs-backoff-limit-per-index-goes-ga/>`__.

-  JobSuccessPolicy (GA)

   JobSuccessPolicy moved to GA. It allows you to use custom success policies for jobs. For example, you can specify whether some indexes are successful and the number of successful indexes to determine whether a job is complete. For details, see `Kubernetes 1.33: Job's SuccessPolicy Goes GA <https://kubernetes.io/blog/2025/05/15/kubernetes-1-33-jobs-success-policy-goes-ga/>`__.

-  MatchLabelKeys (GA)

   MatchLabelKeysInPodAffinity moved to GA. **matchLabelKeys** and **mismatchLabelKeys** have been added to pod affinity rules.

-  NodeInclusionPolicyInPodTopologySpread (GA)

   NodeInclusionPolicyInPodTopologySpread moved to GA. It allows you to use **nodeAffinityPolicy** and **nodeTaintsPolicy** in `pod topology spread constraints <https://kubernetes.io/docs/concepts/scheduling-eviction/topology-spread-constraints/>`__ to dynamically obtain nodes that can accept pods.

   -  **nodeAffinityPolicy** defaults to **Honor**. Only the nodes that match the node selector or node affinity of the pod are included in the topology spread calculation.
   -  **nodeTaintsPolicy** defaults to **Ignore**. This indicates that the node affinity and node selector rules are ignored and all nodes are included in the topology spread calculation.

   For details, see `Pod Topology Spread Constraints <https://kubernetes.io/docs/concepts/scheduling-eviction/topology-spread-constraints/>`__.

-  HonorPVReclaimPolicy (GA)

   HonorPVReclaimPolicy moved to GA. It ensures that when **reclaimPolicy** of a PV is set to **Delete**, the underlying storage resources are deleted based on the policy regardless of the deletion sequence of the PV or PVC. This prevents storage resource leakage.

-  ImageVolume (beta)

   ImageVolume moved to beta. It allows you to use the image volume source in a pod. This volume source lets you mount a container image to the pod as a read-only volume. For details, see `Kubernetes v1.33: Image Volumes graduate to beta! <https://kubernetes.io/blog/2025/04/29/kubernetes-v1-33-image-volume-beta/>`__

-  UserNamespacesSupport (beta)

   UserNamespacesSupport moved to beta. It enables pods to use the Linux user namespaces. For details, see `Use a User Namespace With a Pod <https://kubernetes.io/docs/tasks/configure-pod-container/user-namespaces/>`__.

-  StreamingCollectionEncodingToProtobuf (beta)

   StreamingCollectionEncodingToProtobuf moved to beta. kube-apiserver disables the watch-list strategy and uses streaming encoding instead. This feature gate effectively reduces memory usage and improves system when a large number of resources are requested. For details, see `Kubernetes v1.33: Streaming List responses <https://kubernetes.io/blog/2025/05/09/kubernetes-v1-33-streaming-list-responses/>`__.

-  SchedulerPopFromBackoffQ (beta)

   SchedulerPopFromBackoffQ moved to beta. It optimizes the scheduling queue behavior and allows pods to be directly popped from the backoffQ when the activeQ is empty. This significantly reduces the pod scheduling delay. For details, see `Pop from backoffQ when activeQ is empty <https://github.com/kubernetes/kubernetes/pull/130772>`__.

-  ProcMountType (beta)

   ProcMountType moved to beta. It allows you to use the **securityContext.procMount** field of a pod to customize the mounting type of the **/proc** file system in the container to control the access to **/proc** in a refined manner. This improves pod security and isolation. This feature gate applies to scenarios where non-privileged containers need to run in user namespaces. By relaxing the restrictions on accessing **/proc**, compatibility and flexibility can be enhanced.

-  PodLifecycleSleepAction (beta)

   PodLifecycleSleepAction moved to beta. This feature gate is enabled by default. It allows you to create containers with a hibernation lifecycle of zero seconds. For details, see `Introducing Sleep Action for PreStop Hook <https://github.com/kubernetes/enhancements/blob/master/keps/sig-node/3960-pod-lifecycle-sleep-action/README.md>`__.

-  Limitations on the number of PVCs associated with a specific VolumeAttributesClass

   You are allowed to use ResourceQuota to limit the number of PVCs associated with a specific VolumeAttributesClass.

.. _cce_bulletin_0105__en-us_topic_0000002447366897_section1898982110241:

API Changes and Removals
------------------------

-  **annotation service.kubernetes.io/topology-mode** of EndpointSlices has been deprecated and replaced by **spec.trafficDistribution** to maintain backward compatibility.

-  The apidiscovery.k8s.io/v2beta1 API group has been deprecated. This API is used by a client to obtain information about all registered APIs in a cluster. Use the stable version v2.

-  WatchFromStorageWithoutResourceVersion has been deprecated. This function enables watches based on etcd without **resourceVersion**.

-  The Endpoints API of v1 has been deprecated, but it is still supported. Use the EndpointSlices API instead. The EndpointSlices API has been stable since v1.21 and features such as dual-stack networks have been introduced. For details, see `Kubernetes v1.33: Continuing the transition from Endpoints to EndpointSlices <https://kubernetes.io/blog/2025/04/24/endpoints-deprecation/>`__.

-  The **status.resize** field in pods has been deprecated. It is no longer supported. The status of a resize operation is now exposed via:

   -  **PodResizePending**: indicates that kubelet cannot grant the resize immediately (for example, **reason: Deferred** if temporarily unable or **reason: Infeasible** if impossible on the node).
   -  **PodResizeInProgress**: indicates that the resize is accepted and being applied. Errors encountered during this phase are now reported in this condition's message with **reason: Error**.

   For details, see `Kubernetes v1.33: In-Place Pod Resize Graduated to Beta <https://kubernetes.io/blog/2025/05/16/kubernetes-v1-33-in-place-pod-resize-beta/>`__.

.. _cce_bulletin_0105__en-us_topic_0000002447366897_section115291322132513:

Enhanced Kubernetes 1.33 on CCE
-------------------------------

During a version maintenance period, CCE periodically updates Kubernetes 1.33 and provides enhanced functions.

For details about cluster version updates, see :ref:`Patch Versions <cce_10_0405>`.

.. _cce_bulletin_0105__en-us_topic_0000002447366897_en-us_topic_0000001072975092_en-us_topic_0261805759_en-us_topic_0261793154_section1272182810583:

References
----------

For more details about the performance comparison and functional evolution between Kubernetes 1.33 and other versions, see `Kubernetes v1.33 Release Notes <https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.33.md>`__.
