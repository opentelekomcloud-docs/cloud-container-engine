:original_name: cce_bulletin_0108.html

.. _cce_bulletin_0108:

Kubernetes 1.34 Release Notes
=============================

CCE has passed the Certified Kubernetes Conformance Program and is a certified Kubernetes offering. CCE now supports Kubernetes 1.34 cluster features. This section describes the changes made in Kubernetes 1.34.

Indexes
-------

-  :ref:`New and Enhanced Features <cce_bulletin_0108__en-us_topic_0000002521161917_en-us_topic_0000002486722206_section14972102702312>`
-  :ref:`API Changes and Removals <cce_bulletin_0108__en-us_topic_0000002521161917_en-us_topic_0000002486722206_section1898982110241>`
-  :ref:`Enhanced Kubernetes 1.34 on CCE <cce_bulletin_0108__en-us_topic_0000002521161917_en-us_topic_0000002486722206_section115291322132513>`
-  :ref:`References <cce_bulletin_0108__en-us_topic_0000002521161917_en-us_topic_0000002486722206_en-us_topic_0000001072975092_en-us_topic_0261805759_en-us_topic_0261793154_section1272182810583>`

.. _cce_bulletin_0108__en-us_topic_0000002521161917_en-us_topic_0000002486722206_section14972102702312:

New and Enhanced Features
-------------------------

-  RecoverVolumeExpansionFailure (GA)

   RecoverVolumeExpansionFailure has moved to GA. You can reduce the requested size of a PVC. The requested size can be modified as long as the previous expansion has not yet completed. Kubernetes will automatically adjust the PVC to correct the issue. For details, see `Kubernetes v1.34: Recovery From Volume Expansion Failure (GA) <https://kubernetes.io/blog/2025/09/19/kubernetes-v1-34-recover-expansion-failure/>`__.

-  JobPodReplacementPolicy (GA)

   JobPodReplacementPolicy has moved to GA. This feature introduces the **.spec.podReplacementPolicy** field for jobs. With this setting, you can configure a job to create a replacement pod only after the original pod has completely terminated (**.status.phase: Failed**). For details, see `Kubernetes v1.34: Pod Replacement Policy for Jobs Goes GA <https://kubernetes.io/blog/2025/09/05/kubernetes-v1-34-pod-replacement-policy-for-jobs-goes-ga/>`__.

-  Resilient watch cache initialization (GA)

   The initialization process of the watch cache has been enhanced to be more fault-resistant. This improvement increases the robustness of the cluster control planes and ensures that controllers and clients can reliably establish and maintain observations. For details, see `Resilient watchcache initialization <https://github.com/kubernetes/enhancements/tree/master/keps/sig-api-machinery/4568-resilient-watchcache-initialization>`__.

-  Relaxing DNS search path validation (GA)

   Relaxing DNS search path validation has moved to GA. Previously, Kubernetes verified search strings in **dnsConfig.searches** (the DNS search domain list) based on RFC-1123, which imposes strict restrictions on host names. This validation proved too restrictive for practical use cases of **dnsConfig.searches**. With the new release, the following settings are now supported:

   -  Underscores (_) can be used in place of hyphens (-).
   -  A single dot (.) can be specified as a search domain.

   For details, see `Relaxed DNS search string validation <https://github.com/kubernetes/enhancements/tree/master/keps/sig-network/4427-relaxed-dns-search-validation>`__.

-  Sleep action for container lifecycle hooks (GA)

   Sleep action for container lifecycle hooks has moved to GA. This feature allows you to use the **sleep** command by setting the **Sleep** field in the container lifecycle hooks to pause a container for a specified duration. You can also set **sleep** to **0**. For details, see `Introducing Sleep Action for PreStop Hook <https://github.com/kubernetes/enhancements/tree/master/keps/sig-node/3960-pod-lifecycle-sleep-action>`__ and `Allow zero value for Sleep Action of PreStop Hook <https://github.com/kubernetes/enhancements/blob/master/keps/sig-node/4818-allow-zero-value-for-sleep-action-of-prestop-hook/README.md>`__.

-  Dynamic resource allocation (GA)

   Dynamic resource allocation (DRA) has moved to GA. DRA allows you to request and share resources among pods. For details, see `Dynamic Resource Allocation <https://kubernetes.io/docs/concepts/scheduling-eviction/dynamic-resource-allocation/#about-dra>`__.

-  kuberc file for kubectl user preferences (beta)

   kuberc file for kubectl user preferences has moved to beta. A **kuberc** configuration file allows you to define preferences for **kubectl**, such as default options and command aliases. For details, see `Kubernetes v1.34: User preferences (kuberc) are available for testing in kubectl 1.34 <https://kubernetes.io/blog/2025/08/28/kubernetes-v1-34-kubectl-kuberc-beta/>`__ .

-  Snapshottable API server cache (beta)

   Snapshottable API server cache has moved to beta. With **ListFromCacheSnapshot** enabled by default, kube-apiserver attempts to serve responses directly from snapshots. For details, see `Kubernetes v1.34: Snapshottable API server cache <https://kubernetes.io/blog/2025/09/09/kubernetes-v1-34-snapshottable-api-server-cache/>`__.

-  KubeletPSI (beta)

   KubeletPSI has moved to beta. Pressure Stall Information (PSI) is a monitoring metric that shows how much pressure the system is under in terms of CPU, memory, and I/O resources. It helps you understand information such as service latency and overall system stability.

   kubelet now allows you to obtain a node's pressure metrics. For details, see `Understand Pressure Stall Information (PSI) Metrics <https://kubernetes.io/docs/reference/instrumentation/understand-psi-metrics/>`__.

-  KubeletPodResourcesGet and KubeletPodResourcesDynamicResources (beta)

   KubeletPodResourcesGet and KubeletPodResourcesDynamicResources have moved to beta. New gRPC APIs are now available. They allow you to obtain resource allocation details through the kubelet. For details, see `Device Plugins <https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/device-plugins/>`__.

-  PodLevelResources (beta)

   PodLevelResources has moved to beta. This feature gate enables pod-level resource management, allowing Kubernetes to control the total resource usage of pods and reducing the need to fine-tune resource settings for each individual container. For details, see `Kubernetes v1.34: Pod Level Resources Graduated to Beta <https://kubernetes.io/blog/2025/09/22/kubernetes-v1-34-pod-level-resources/>`__.

   .. note::

      When you configure resource requests or limits for pods, the CPU and memory information displayed on the console, including requests, limits, and usage, is still calculated based on the container-level values.

.. _cce_bulletin_0108__en-us_topic_0000002521161917_en-us_topic_0000002486722206_section1898982110241:

API Changes and Removals
------------------------

-  When creating a headless Service, if **loadBalancerIP**, **externalIPs**, or **sessionAffinity** is set, a warning message is now displayed.
-  If a container does not specify hugepage resources, pod-level hugepage cgroups are propagated to the container. Verification has been added to ensure that the aggregated container hugepage limit is less than or equal to the pod-level limits.
-  CRDs now support the OpenAPI format for both Kubernetes short names and long names.
-  The AuthenticationConfiguration type supported by **--authentication-config** in the kube-apiserver configuration files has been upgraded to **apiserver.config.k8s.io/v1**.
-  The JobPodReplacementPolicy feature gate was locked to **true** and will be removed in a future Kubernetes release.
-  The AuthorizeWithSelectors and AuthorizeNodeWithSelectors feature gates are now stable and locked on.
-  The fallback behavior of the Downward API's **resourceFieldRef** field has been updated to consider pod-level resources. If a container-level resource limit is not configured, the pod-level resource limit will be used first before falling back to node allocatable resources.
-  Zero-valued **metadata.creationTimestamp** is now omitted and no longer serialized as an explicit **null** in JSON, YAML, or CBOR output.

.. _cce_bulletin_0108__en-us_topic_0000002521161917_en-us_topic_0000002486722206_section115291322132513:

Enhanced Kubernetes 1.34 on CCE
-------------------------------

During a version maintenance period, CCE periodically updates Kubernetes 1.34 and provides enhanced functions.

For details about cluster version updates, see :ref:`Patch Versions <cce_10_0405>`.

.. _cce_bulletin_0108__en-us_topic_0000002521161917_en-us_topic_0000002486722206_en-us_topic_0000001072975092_en-us_topic_0261805759_en-us_topic_0261793154_section1272182810583:

References
----------

For more details about the performance comparison and functional evolution between Kubernetes 1.34 and other versions, see `Kubernetes v1.34 Release Notes <https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.34.md>`__.
