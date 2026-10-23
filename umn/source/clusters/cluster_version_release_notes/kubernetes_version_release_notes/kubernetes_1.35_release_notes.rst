:original_name: cce_bulletin_0117.html

.. _cce_bulletin_0117:

Kubernetes 1.35 Release Notes
=============================

This section describes the changes in Kubernetes 1.35.

Indexes
-------

-  :ref:`New and Enhanced Features <cce_bulletin_0117__en-us_topic_0000002569355841_section14972102702312>`
-  :ref:`API Changes and Removals <cce_bulletin_0117__en-us_topic_0000002569355841_section1898982110241>`
-  :ref:`Enhanced Kubernetes 1.35 on CCE <cce_bulletin_0117__en-us_topic_0000002569355841_section115291322132513>`
-  :ref:`References <cce_bulletin_0117__en-us_topic_0000002569355841_en-us_topic_0000001072975092_en-us_topic_0261805759_en-us_topic_0261793154_section1272182810583>`

.. _cce_bulletin_0117__en-us_topic_0000002569355841_section14972102702312:

New and Enhanced Features
-------------------------

-  PreferSameNode traffic distribution (GA)

   In Kubernetes 1.35, this feature has graduated to GA. The PreferSameNode policy is added, which allows service traffic to be preferentially forwarded to the endpoints on the same node as the client. The PreferClose policy is deprecated and replaced with PreferSameNode. For details, see `PreferSameZone and PreferSameNode Traffic Distribution <https://github.com/kubernetes/enhancements/blob/master/keps/sig-network/3015-prefer-same-node/README.md>`__.

-  Support for the managedBy field for Jobs (GA)

   In Kubernetes 1.35, the support for the **managedBy** field for Jobs has graduated to GA. This feature introduces the **.spec.managedBy** field for Jobs. You can use this field to allow an external controller (such as Kueue or MultiKueue) to take full responsibility for the synchronization logic and status update of a Job. For details, see `Job Managed By Goes GA <https://kubernetes.io/blog/2025/12/18/kubernetes-v1-35-job-managedby-for-jobs-goes-ga/>`__.

-  Pod generation (GA)

   In Kubernetes 1.35, Pod generation has graduated to GA. This feature leverages **metadata.generation** that automatically increments the value when the pod spec is updated. Additionally, the **status.observedGeneration** field is added, which is reported by kubelet to identify the observed pod configuration version. For details, see `Pod Generation <https://github.com/kubernetes/enhancements/tree/master/keps/sig-node/5067-pod-generation>`__.

-  TopologyManager max-allowable-numa-nodes (GA)

   In Kubernetes 1.35, this feature has graduated to GA. The **max-allowable-numa-nodes** policy option is added for Topology Manager. It allows users to specify the maximum number of NUMA nodes. Before this option is added, the maximum number of NUMA nodes that Topology Manager allows is 8. With more than 8 NUMA nodes, high-end CPUs and AI acceleration chips can be used. For details, see `TopologyManager NUMA Node Limit Configuration <https://github.com/kubernetes/enhancements/blob/master/keps/sig-node/4622-topologymanager-max-allowable-numa-nodes/README.md>`__.

-  Node topology downward API (beta)

   In Kubernetes 1.35, Node Topology Downward API has graduated to Beta. This feature uses the built-in PodTopologyLabels admission controller to synchronize standard topology labels of nodes to pods. Containers can directly obtain topology information such as regions, AZs, and host names through the downward API without requiring privileged init containers. For details, see `Node Topology Labels via Downward API <https://github.com/kubernetes/enhancements/blob/master/keps/sig-node/4742-node-topology-downward-api/README.md>`__.

-  Storage version migrator in-tree (beta)

   In Kubernetes 1.35, this feature has graduated to beta. This feature moves the Storage Version Migrator (SVM) from an external component to kube-controller-manager, providing a standard StorageVersionMigration API. For details, see `Move Storage Version Migrator in-tree <https://github.com/kubernetes/enhancements/blob/master/keps/sig-api-machinery/4192-svm-in-tree/README.md>`__.

-  Mutable CSI node allocatable count (beta)

   In Kubernetes 1.35, this feature has graduated to beta. This feature allows dynamic modification of the **CSINode.Spec.Drivers[*].Allocatable.Count** field. When volume mounting fails due to insufficient capacity, the capacity information is automatically corrected to prevent the scheduler from suspending pods based on outdated data. For details, see `Mutable CSINode Allocatable Property <https://github.com/kubernetes/enhancements/blob/master/keps/sig-storage/4876-mutable-csinode-allocatable/README.md>`__.

-  Opportunistic batching (beta)

   In Kubernetes 1.35, the opportunistic batching is added to the scheduler. This feature reuses scheduling results through pod scheduling signatures to improve the efficiency of scheduling batches of similar pods. For details, see `Opportunistic batching <https://github.com/kubernetes/enhancements/blob/master/keps/sig-scheduling/5598-opportunistic-batching/README.md>`__.

-  StatefulSet maxUnavailable (beta)

   In Kubernetes 1.35, this feature has graduated to beta. The **maxUnavailable** field is added to the StatefulSet rolling update policies to specify the maximum number of unavailable pods during an update (either as a number or a percentage). For details, see `Implement maxUnavailable in StatefulSet <https://github.com/kubernetes/enhancements/blob/master/keps/sig-apps/961-maxunavailable-for-statefulset/README.md>`__.

-  KYAML (beta)

   In Kubernetes 1.35, KYAML has graduated to beta. The KYAML output format (-o kyaml), compatible with the standard YAML parsers, is added to kubectl. For details, see `Introducing KYAML, a safer, less ambiguous YAML subset/encoding <https://github.com/kubernetes/enhancements/blob/master/keps/sig-cli/5295-kyaml/README.md>`__.

-  Configurable HPA tolerance (beta)

   In Kubernetes 1.35, this feature has graduated to beta. This feature adds the tolerance configuration item to the native HPA scaling policies. For details, see `Configurable tolerance for HPA <https://github.com/kubernetes/enhancements/blob/master/keps/sig-autoscaling/4951-configurable-hpa-tolerance/README.md>`__.

-  User namespaces (beta)

   In Kubernetes 1.35, this feature has graduated to beta. This feature enables user namespace isolation using the **pod.spec.hostUsers** field, improving pod security isolation. For details, see `Support User Namespaces <https://github.com/kubernetes/enhancements/blob/master/keps/sig-node/127-user-namespaces/README.md>`__.

-  OCI volume source (beta)

   In Kubernetes 1.35, this feature adds image volumes for pods, so users can directly mount OCI images or artifacts as read-only volumes. To use this feature, containerd must be v2.1 or later. For details, see `OCI VolumeSource <https://github.com/kubernetes/enhancements/tree/master/keps/sig-node/4639-oci-volume-source>`__.

-  Secret pulled images (beta)

   In Kubernetes 1.35, this feature has graduated to beta. This feature enables kubelet to cache image pull credentials and enforce access permission verification. It checks whether a pod has permission to use existing images on the node based on the value (IfNotPresent or Never) of **imagePullPolicy**. For details, see `Ensure Secret Pulled Images <https://github.com/kubernetes/enhancements/blob/master/keps/sig-node/2535-ensure-secret-pulled-images/README.md>`__.

-  Container restart rules (beta)

   In Kubernetes 1.35, this feature has graduated to beta. This feature adds the restartPolicy and restartPolicyRules configurations for containers. These configurations can override the pod-level restart policy. For details, see `Container Restart Rules <https://github.com/kubernetes/enhancements/blob/master/keps/sig-node/5307-container-restart-policy/README.md>`__.

-  CSI driver service account tokens via secrets field (beta)

   In Kubernetes 1.35, the **serviceAccountTokenInSecrets** field in the CSI driver spec can be set to **true**. This enables service account tokens to be passed through a dedicated **Secrets** field of the CSI request instead of the **volume_context** field, preventing sensitive token leakage. For details, see `CSI driver opt-in for service account tokens via secrets field <https://github.com/kubernetes/enhancements/blob/master/keps/sig-storage/5538-csi-sa-tokens-secrets-field/README.md>`__.

-  Consider Terminating pods in Deployments (beta)

   In Kubernetes 1.35, this feature has graduated to beta. The **.status.terminatingReplicas** field is added for Deployments and ReplicaSets to count the number of terminating pods. For details, see `Consider Terminating Pods in Deployments <https://github.com/kubernetes/enhancements/blob/master/keps/sig-apps/3973-consider-terminating-pods-deployment/README.md>`__.

.. _cce_bulletin_0117__en-us_topic_0000002569355841_section1898982110241:

API Changes and Removals
------------------------

-  In Kubernetes 1.35 and later versions, cgroup v1 is marked as deprecated and is not supported by default. CCE clusters v1.35 support `cgroup v2 <https://kubernetes.io/docs/concepts/architecture/cgroups/>`__ and are compatible with the OSs of cgroup v1 nodes.

-  Since Kubernetes 1.35, the ipvs mode of kube-proxy has been marked as deprecated and is planned to be removed in future versions. CCE clusters v1.35 and later support and use nftables for forwarding by default.

-  Kubernetes 1.35 is the last version that supports containerd 1.x. From CCE clusters v1.35, containerd 2.x is used by default.

-  In Kubernetes 1.35, ObservedGeneration is added to the **conditions** field of CustomResourceDefinition.

-  In Kubernetes 1.35, the **StorageVersionMigration v1beta1** API is added and the **v1alpha1** API is removed. Before an upgrade, all **v1alpha1** resources must be deleted.

-  In Kubernetes 1.35, the kuberc configuration architecture is updated, and two optional configuration fields, **credPluginPolicy** and **credPluginAllowlist**, are added.

-  In Kubernetes 1.35, the core DRA feature gate is enabled by default and cannot be disabled.

-  In Kubernetes 1.35, **kubectl get -o kyaml** is enabled by default. You can set **KUBECTL_KYAML** to **false** to disable this behavior.

-  In Kubernetes 1.35, in-place adjustment of pod-level resources is enabled. The **Resources** and **AllocatedResources** fields are added to **PodStatus** to record pod-level cgroup resources and requested resources, respectively.

-  In Kubernetes 1.35, the **status.terminatingReplicas** statistics feature for ReplicaSets and Deployments has been upgraded to beta and is enabled by default.

   In Kubernetes 1.35, the JobManagedBy feature has been upgraded to GA. The feature gate is locked to be **true** and will be removed in later versions.

.. _cce_bulletin_0117__en-us_topic_0000002569355841_section115291322132513:

Enhanced Kubernetes 1.35 on CCE
-------------------------------

During a version maintenance period, CCE periodically updates Kubernetes 1.35 with enhanced functions.

For details about cluster version updates, see :ref:`Patch Versions <cce_10_0405>`.

.. _cce_bulletin_0117__en-us_topic_0000002569355841_en-us_topic_0000001072975092_en-us_topic_0261805759_en-us_topic_0261793154_section1272182810583:

References
----------

For more details about the performance comparison and functional enhancements between Kubernetes 1.35 and other versions, see `Kubernetes 1.35 Release Notes <https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.35.md>`__.
