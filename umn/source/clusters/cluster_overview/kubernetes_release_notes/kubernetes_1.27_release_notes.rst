:original_name: cce_bulletin_0059.html

.. _cce_bulletin_0059:

Kubernetes 1.27 Release Notes
=============================

CCE allows you to create clusters of Kubernetes 1.27. This section describes the changes made in Kubernetes 1.27 compared with Kubernetes 1.25.

Indexes
-------

-  :ref:`New Features <cce_bulletin_0059__en-us_topic_0000001605219426_en-us_topic_0000001649273541_section14972102702312>`
-  :ref:`Deprecations and Removals <cce_bulletin_0059__en-us_topic_0000001605219426_en-us_topic_0000001649273541_section1898982110241>`
-  :ref:`Enhanced Kubernetes 1.27 on CCE <cce_bulletin_0059__en-us_topic_0000001605219426_en-us_topic_0000001649273541_section115291322132513>`
-  :ref:`References <cce_bulletin_0059__en-us_topic_0000001605219426_en-us_topic_0000001649273541_en-us_topic_0000001072975092_en-us_topic_0261805759_en-us_topic_0261793154_section1272182810583>`

.. _cce_bulletin_0059__en-us_topic_0000001605219426_en-us_topic_0000001649273541_section14972102702312:

New Features
------------

**Kubernetes 1.27**

-  SeccompDefault is stable.

   To use SeccompDefault, add the **--seccomp-default** `command line flag <https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/>`__ using kubelet on each node. If this feature is enabled, the **RuntimeDefault** profile will be used for all workloads by default, instead of the **Unconfined** (seccomp disabled) profile.

-  Jobs' scheduling directives are configurable.

   This feature was introduced in Kubernetes 1.22 and is stable in Kubernetes 1.27. In most cases, you use a Job to influence where the pods will run, like all in the same AZ. This feature allows scheduling directives to be modified before a Job starts. You can use the **suspend** field to suspend a Job. In the suspension phase, the scheduling directives (such as the node selector, node affinity, anti-affinity, and tolerations) in the Job's pod template can be modified. For details, see `Mutable Scheduling Directives <https://kubernetes.io/docs/concepts/workloads/controllers/job/#mutable-scheduling-directives>`__.

-  Downward API hugepages are stable.

   In Kubernetes 1.20, **requests.hugepages-**\ *<pagesize>* and **limits.hugepages-**\ *<pagesize>* were introduced to the `downward API <https://kubernetes.io/docs/concepts/workloads/pods/downward-api/>`__. Requests and limits can be configured for hugepages like other resources.

-  Pod scheduling readiness moves to beta.

   After a pod is created, the Kubernetes scheduler selects an appropriate node to run the pod in the pending state. In practice, some pods may stay in the pending state for a long period due to insufficient resources. These pods may affect the running of other components like Cluster Autoscaler in the cluster. By specifying or deleting **.spec. schedulingGates** for a pod, you can control when the pod is ready for scheduling. For details, see `Pod Scheduling Readiness <https://kubernetes.io/docs/concepts/scheduling-eviction/pod-scheduling-readiness/>`__.

-  Accessing node logs using Kubernetes APIs is supported.

   This function is in the alpha phase. The cluster administrator can directly query node logs to help debug malfunctioning services running on the node. To use this function, ensure that the NodeLogQuery `feature gate <https://kubernetes.io/docs/reference/command-line-tools-reference/feature-gates/>`__ is enabled for that node and the kubelet configuration options **enableSystemLogHandler** and **enableSystemLogQuery** are set to **true**.

-  ReadWriteOncePod access mode moves to beta.

   Kubernetes 1.22 introduced a ReadWriteOncePod access mode for PVs and PVCs. This feature has evolved into the beta phase. A volume can be mounted to a single pod in read/write mode. Use this access mode if you want to ensure that only one pod in the cluster can read that PVC or write to it. For details, see `Access Modes <https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes>`__.

-  The **matchLabelKeys** field in the pod topology spread constraint moves to beta.

   **matchLabelKeys** is a list of pod label keys. It is used to select a group of pods over which spreading will be calculated. With **matchLabelKeys**, you do not need to update **pod.spec** between different revisions. The controller or operator just needs to set different values to the same label key for different revisions. The scheduler will automatically determine the values based on **matchLabelKeys**. For details, see `Pod Topology Distribution Constraints <https://kubernetes.io/docs/concepts/scheduling-eviction/topology-spread-constraints/#topologyspreadconstraints-field>`__.

-  The function of efficiently labeling SELinux volumes moves to beta.

   By default, the container runtime recursively assigns the SELinux label to all files on all pod volumes. To speed up this process, Kubernetes uses the mount option **-o context=**\ *<label>* to immediately change the SELinux label of the volume. For details, see `Efficient SELinux volume relabeling <https://kubernetes.io/docs/tasks/configure-pod-container/security-context/#efficient-selinux-volume-relabeling>`__.

-  VolumeManager reconstruction goes to beta.

   After the VolumeManager is reconstructed, if the NewVolumeManagerReconstruction `feature gate <https://kubernetes.io/docs/reference/command-line-tools-reference/feature-gates/>`__ is enabled, mounted volumes will be obtained in a more effective way during kubelet startup.

-  Server side field validation and OpenAPI V3 are stable.

   OpenAPI V3 was added in Kubernetes 1.23. In Kubernetes 1.24, it moved to beta. In Kubernetes 1.27, it is stable.

-  StatefulSet start ordinal moves to beta.

   Kubernetes 1.26 introduced a new, alpha-level feature for StatefulSets to control the ordinal numbering of pod replicas. Since Kubernetes 1.27, this feature moves to beta. The ordinals can start from arbitrary non-negative numbers. For details, see `Kubernetes 1.27: StatefulSet Start Ordinal Simplifies Migration <https://kubernetes.io/blog/2023/04/28/statefulset-start-ordinal/>`__.

-  **ContainerResource** metric in HorizontalPodAutoscaler moves to beta.

   Kubernetes 1.20 introduced the `ContainerResource <https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/#container-resource-metrics>`__ metric in HorizontalPodAutoscaler (HPA). In Kubernetes 1.27, this feature moves to beta, and the HPAContainerMetrics feature gate is enabled by default.

-  StatefulSet PVC auto deletion moves to beta.

   Kubernetes 1.27 provides a new policy to control the lifecycle of PVCs of StatefulSets. This policy allows users to specify if the PVCs generated from the StatefulSet spec template should be automatically deleted or retrained when the StatefulSet is deleted or replicas in the StatefulSet are scaled down. For details, see `PersistentVolumeClaim retention <https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/#persistentvolumeclaim-retention>`__.

-  Volume group snapshots are introduced.

   Volume group snapshots are introduced as an alpha feature in Kubernetes 1.27. This feature allows users to create snapshots for multiple volumes to ensure data consistency when a fault occurs. It uses a label selector to group multiple PVCs for snapshot. This feature only supports CSI volume drivers. For details, see `Kubernetes 1.27: Introducing an API for Volume Group Snapshots <https://kubernetes.io/blog/2023/05/08/kubernetes-1-27-volume-group-snapshot-alpha/>`__.

-  **kubectl apply** pruning is more secure and efficient.

   In Kubernetes 1.5, the **--prune** flag was introduced in **kubectl apply** to delete resources that are no longer needed. This allowed **kubectl apply** to automatically clear resources removed from the current configuration. However, the existing implementation of **--prune** has design defects that degrade its performance and lead to unexpected behaviors. In Kubernetes 1.27, **kubectl apply** provides ApplySet-based pruning, which is in the alpha phase. For details, see `Declarative Management of Kubernetes Objects Using Configuration Files <https://kubernetes.io/docs/tasks/manage-kubernetes-objects/declarative-config/#alternative-kubectl-apply-f-directory-prune>`__.

-  Conflicts during port allocation to NodePort Service can be avoided.

   In Kubernetes 1.27, you can enable a new `feature gate <https://kubernetes.io/docs/reference/command-line-tools-reference/feature-gates/>`__ ServiceNodePortStaticSubrange to use different port allocation policies for NodePort Services. This mitigates the risk of port conflicts. This feature is in the alpha phase.

-  Resizing resources assigned to pods without restarting the containers is supported.

   Kubernetes 1.27 allows users to resize CPU and memory resources assigned to pods without restarting the container. This feature is in the alpha phase. For details, see `Kubernetes 1.27: In-place Resource Resize for Kubernetes Pods (alpha) <https://kubernetes.io/blog/2023/05/12/in-place-pod-resize-alpha/>`__.

-  Pod startup is accelerated.

   A series of parameter adjustments like parallel image pulls and increased default API query limit for kubelet per second are made in Kubernetes 1.27 to accelerate pod startup. For details, see `Kubernetes 1.27: updates on speeding up Pod startup <https://kubernetes.io/blog/2023/05/15/speed-up-pod-startup/>`__.

-  KMS V2 moves to beta.

   The key management KMS V2 API goes to beta. This has greatly improved the performance of the KMS encryption provider. For details, see `Using a KMS provider for data encryption <https://kubernetes.io/docs/tasks/administer-cluster/kms-provider/>`__.

**Kubernetes 1.26**

-  CRI v1alpha2 is removed.

   Kubernetes 1.26 does not support CRI v1alpha2 any longer. Use CRI v1 (containerd version must be later than or equal to 1.5.0). containerd 1.5.x or earlier is not supported by Kubernetes 1.26. Update the containerd version to 1.6.x or later before upgrading kubelet to 1.26.

   .. note::

      The containerd version used by CCE is 1.6.14, which meets the requirements. If the existing nodes do not meet the containerd version requirements, reset them to the latest version.

-  Alpha API for dynamic resource allocation is added.

   In Kubernetes 1.26, `Dynamic Resource Allocation <https://kubernetes.io/docs/concepts/scheduling-eviction/dynamic-resource-allocation/>`__ is added to request and share resources between pods and between containers in a pod. Resources can be initialized based on parameters provided by the user. This function is still in the alpha phase. You need to enable the DynamicResourceAllocation feature gate and the **resource.k8s.io/v1alpha1** API group. You need to install drivers for specific resources to be managed. For details, see `Kubernetes 1.26: Alpha API for Dynamic Resource Allocation <https://kubernetes.io/blog/2022/12/15/dynamic-resource-allocation/>`__.

-  The non-graceful node shutdown feature goes to beta.

   In Kubernetes 1.26, the non-graceful node shutdown feature goes to beta and is enabled by default. A node shutdown can be graceful only if the kubelet's node shutdown manager can detect the upcoming node shutdown action. For details, see `Non-graceful node shutdown handling <https://kubernetes.io/docs/concepts/architecture/nodes/#non-graceful-node-shutdown>`__.

-  Passing pod **fsGroup** to CSI drivers during mounting is supported.

   In Kubernetes 1.22, delegation of **fsGroup** to CSI drivers was first introduced as an alpha feature. In Kubernetes 1.25, it moved to beta. In Kubernetes 1.26, this feature enters the official release phase. For details, see `Delegating volume permission and ownership change to CSI driver <https://kubernetes.io/docs/tasks/configure-pod-container/security-context/#delegating-volume-permission-and-ownership-change-to-csi-driver>`__.

-  Pod scheduling readiness is introduced.

   Kubernetes 1.26 introduces a new feature schedulingGates, which enables the scheduler to detect when pod scheduling can be performed. For details, see `Pod Scheduling Readiness <https://kubernetes.io/docs/concepts/scheduling-eviction/pod-scheduling-readiness/>`__.

-  CPU manager is officially released.

   The CPU manager is a part of kubelet. Since Kubernetes 1.10, it has moved to `beta <https://kubernetes.io/blog/2018/07/24/feature-highlight-cpu-manager/>`__. The CPU manager can allocate exclusive CPUs to containers. This feature is stable in Kubernetes 1.26. For details, see `Control CPU Management Policies on the Node <https://kubernetes.io/docs/tasks/administer-cluster/cpu-management-policies/>`__.

-  Kubernetes traffic engineering is advanced.

   `Internal node-local traffic optimization <https://kubernetes.io/blog/2022/12/30/advancements-in-kubernetes-traffic-engineering/#optimizing-internal-node-local-traffic>`__ and `EndpointSlice conditions <https://kubernetes.io/blog/2022/12/30/advancements-in-kubernetes-traffic-engineering/#endpointslice-conditions>`__ are upgraded to the official release version. `ProxyTerminatingEndpoints <https://kubernetes.io/blog/2022/12/30/advancements-in-kubernetes-traffic-engineering/#traffic-loss-from-load-balancers-during-rolling-updates>`__ moves to beta.

-  Cross-namespace volume data sources are supported.

   This feature allows you to specify a data source that belongs to different namespaces for a PVC. This feature is in the alpha phase. For details, see `Cross namespace data sources <https://kubernetes.io/docs/concepts/storage/persistent-volumes/#cross-namespace-data-sources>`__.

-  Retroactive default StorageClass assignment moves to beta.

   In Kubernetes 1.25, an alpha feature was introduced to change the way how a default StorageClass is allocated to a PVC. After this feature is enabled, you no longer need to create a default StorageClass and then create a PVC to assign the class. Additionally, any PVCs without a StorageClass assigned can be updated later. This feature moves to beta in Kubernetes 1.26. For details, see `Retroactive default StorageClass assignment <https://kubernetes.io/docs/concepts/storage/persistent-volumes/#retroactive-default-storageclass-assignment>`__.

-  PodDisruptionBudget allows users to specify the eviction policies for unhealthy pods.

   You are allowed to specify unhealthy pod eviction policies for `PodDisruptionBudget <https://kubernetes.io/docs/concepts/workloads/pods/disruptions/#pod-disruption-budgets>`__ (PDB). This feature helps ensure node availability during node management. This feature is in the beta phase. For details, see `Unhealthy Pod Eviction Policy <https://kubernetes.io/docs/tasks/run-application/configure-pdb/#unhealthy-pod-eviction-policy>`__.

-  The number of Horizontal Pod Autoscaler (HPA) can be configured.

   **kube-controller-manager** allows **--concurrent-horizontal-pod-autoscaler-syncs** to configure the number of worker nodes of the pod autoscaler for horizontal scaling.

.. _cce_bulletin_0059__en-us_topic_0000001605219426_en-us_topic_0000001649273541_section1898982110241:

Deprecations and Removals
-------------------------

**Kubernetes 1.27**

-  In Kubernetes 1.27, the feature gates that are used for volume extension and in the General Availability (GA) status, including ExpandCSIVolumes, ExpandInUsePersistentVolumes, and ExpandPersistentVolumes are removed and can no longer be referenced in the **--feature-gates** flag.
-  The **--master-service-namespace** parameter is removed. This parameter specifies where to create a Service named **kubernetes** to represent the API server. This parameter was deprecated in Kubernetes 1.26 and is removed from Kubernetes 1.27.
-  The ControllerManagerLeaderMigration feature gate is removed. `Leader Migration <https://github.com/kubernetes/enhancements/issues/2436>`__ provides a mechanism for HA clusters to safely migrate "cloud specific" controllers using a resource lock shared between kube-controller-manager and cloud-controller-manager when upgrading the replicated control plane. This feature has been enabled unconditionally since its release in Kubernetes 1.24. In Kubernetes 1.27, this feature is removed.
-  The **--enable-taint-manager** parameter is removed. The feature that it supports, taint-based eviction, is enabled by default and will continue to be implicitly enabled when the flag is removed.
-  The **--pod-eviction-timeout** parameter is removed from kube-controller-manager.
-  The CSIMigration feature gate is removed. The `CSI migration <https://github.com/kubernetes/enhancements/issues/625>`__ program allows smooth migration from the in-tree volume plug-ins to the out-of-tree CSI drivers. This feature was officially released in Kubernetes 1.16.
-  The CSIInlineVolume feature gate is removed. The feature (`CSI Ephemeral Volume <https://github.com/kubernetes/kubernetes/pull/111258>`__) allows CSI volumes to be specified directly in the pod specification for ephemeral use cases. They can be used to inject arbitrary states, such as configuration, secrets, identity, variables, or similar information, directly inside the pod using a mounted volume. This feature graduated to GA in Kubernetes 1.25 and is removed in Kubernetes 1.27.
-  The EphemeralContainers feature gate is removed. For Kubernetes 1.27, API support for ephemeral containers is unconditionally enabled.
-  The LocalStorageCapacityIsolation feature gate is removed. This feature gate (`Local Ephemeral Storage Capacity Isolation <https://github.com/kubernetes/kubernetes/pull/111513>`__) moved to GA in Kubernetes 1.25. The feature provides support for capacity isolation of local ephemeral storage between pods, such as emptyDir volumes, so that a pod can be limited in its consumption of shared resources. The kubelet will evict a pod if its consumption of local ephemeral storage exceeds the configured limit.
-  The NetworkPolicyEndPort feature gate is removed. In Kubernetes 1.25, **endPort** in NetworkPolicy moved to GA. NetworkPolicy providers that support the **endPort** field can be used to specify a range of ports to apply NetworkPolicy.
-  The StatefulSetMinReadySeconds feature gate is removed. For a pod that is part of a StatefulSet, Kubernetes marks the pod as read-only when the pod is available (and passes the check) at least within the period specified in the `minReadySeconds <https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/#minimum-ready-seconds>`__. This feature was officially released in Kubernetes 1.25. It is locked to **true** and removed from Kubernetes 1.27.
-  The IdentifyPodOS feature gate is removed. If this feature is enabled, you can specify an OS for a pod. It has been stable since Kubernetes 1.25. This feature is removed from Kubernetes 1.27.
-  The DaemonSetUpdateSurge feature gate is removed. In Kubernetes 1.25, this feature was stable. It was implemented to minimize DaemonSet downtime during deployment, but it is removed from Kubernetes 1.27.
-  The **--container-runtime** parameter is removed. kubelet accepts a deprecated parameter **--container-runtime**, and the only valid value will be **remote** after the dockershim code is removed. This parameter was deprecated in 1.24 and later versions and is removed from Kubernetes 1.27.

**Kubernetes 1.26**

-  HorizontalPodAutoscaler API for v2beta2 is removed.

   The autoscaling/v2beta2 API of HorizontalPodAutoscaler is no longer available in Kubernetes 1.26. For details, see `Removed APIs by release <https://kubernetes.io/docs/reference/using-api/deprecation-guide/#horizontalpodautoscaler-v126>`__. Use autoscaling/v2 API instead.

-  The **flowcontrol.apiserver.k8s.io/v1beta1** API is removed.

   In Kubernetes 1.26 and later versions, the API of the **flowcontrol.apiserver.k8s.io/v1beta1** version for FlowSchema and PriorityLevelConfiguration is no longer served. For details, see `Removed APIs by release <https://kubernetes.io/docs/reference/using-api/deprecation-guide/#horizontalpodautoscaler-v126>`__. The **flowcontrol.apiserver.k8s.io/v1beta2** version is available in Kubernetes 1.23 and later versions, and the **flowcontrol.apiserver.k8s.io/v1beta3** version is available in Kubernetes 1.26 and later versions.

-  The cloud service vendors' in-tree storage drivers are removed.

-  The kube-proxy userspace mode is removed.

   The deprecated userspace mode is no longer supported by Linux or Windows. Linux users can use Iptables or IPVS, and Windows users can use the Kernelspace mode. Errors are returned if you use **--mode userspace**.

   -  Windows winkernel kube-proxy no longer supports Windows HNS v1 APIs.

-  **--prune-whitelist** flag is deprecated.

   The **--prune-whitelist** flag is `deprecated <https://github.com/kubernetes/kubernetes/pull/113116>`__ and replaced by **--prune-allowlist** to support `Inclusive Naming Initiative <https://www.cncf.io/announcements/2021/10/13/inclusive-naming-initiative-announces-new-community-resources-for-a-more-inclusive-future/>`__. This deprecated flag will be completely removed in later versions.

-  The DynamicKubeletConfig feature gate is removed.

   The kubelet configuration of nodes can be dynamically updated through the API. The feature gate is removed from the kubelet in Kubernetes 1.24 and removed from the API server in Kubernetes 1.26. This simplifies the code and improves stability. It is recommended that you modify the kubelet configuration file instead and then restart the kubelet. For details, see `Remove DynamicKubeletConfig feature gate from the code <https://github.com/kubernetes/kubernetes/pull/112643>`__.

-  A kube-apiserver command line parameter is removed.

   The `--master-service-namespace <https://github.com/kubernetes/kubernetes/pull/38186>`__ parameter is deprecated. It is unused in the API Server.

-  Several **kubectl run** parameters are deprecated.

   Several unused kubectl subcommands are marked as `deprecated <https://github.com/kubernetes/kubernetes/pull/112261>`__ and will be removed in later versions. These subcommands include **--cascade**, **--filename**, **--force**, **--grace-period**, **--kustomize**, **--recursive**, **--timeout**, and **--wait**.

-  Some command line parameters related to logging are removed.

   Some logging-related command line parameters are `removed <https://github.com/kubernetes/kubernetes/pull/112120>`__. These parameters were `deprecated <https://github.com/kubernetes/enhancements/tree/3cb66bd0a1ef973ebcc974f935f0ac5cba9db4b2/keps/sig-instrumentation/2845-deprecate-klog-specific-flags-in-k8s-components#removed-klog-flags>`__ in earlier versions.

.. _cce_bulletin_0059__en-us_topic_0000001605219426_en-us_topic_0000001649273541_section115291322132513:

Enhanced Kubernetes 1.27 on CCE
-------------------------------

During a version maintenance period, CCE periodically updates Kubernetes 1.27 and provides enhanced functions.

For details about cluster version updates, see :ref:`Release Notes for CCE Cluster Versions <cce_10_0405>`.

.. _cce_bulletin_0059__en-us_topic_0000001605219426_en-us_topic_0000001649273541_en-us_topic_0000001072975092_en-us_topic_0261805759_en-us_topic_0261793154_section1272182810583:

References
----------

For more details about the performance comparison and function evolution between Kubernetes 1.27 and other versions, see the following documents:

-  `Kubernetes v1.27 Release Notes <https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.27.md>`__
-  `Kubernetes v1.26 Release Notes <https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.26.md>`__
