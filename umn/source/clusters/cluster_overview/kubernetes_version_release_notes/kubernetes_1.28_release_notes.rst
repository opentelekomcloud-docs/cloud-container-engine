:original_name: cce_bulletin_0068.html

.. _cce_bulletin_0068:

Kubernetes 1.28 Release Notes
=============================

CCE allows you to create Kubernetes clusters 1.28. This section describes the changes made in Kubernetes 1.28.

Indexes
-------

-  :ref:`Important Notes <cce_bulletin_0068__en-us_topic_0000001851583716_section611420132265>`
-  :ref:`New and Enhanced Features <cce_bulletin_0068__en-us_topic_0000001851583716_section14972102702312>`
-  :ref:`API Changes and Removals <cce_bulletin_0068__en-us_topic_0000001851583716_section1898982110241>`
-  :ref:`Feature Gate and Command Line Parameter Changes and Removals <cce_bulletin_0068__en-us_topic_0000001851583716_section7619242123210>`
-  :ref:`Enhanced Kubernetes 1.28 on CCE <cce_bulletin_0068__en-us_topic_0000001851583716_section115291322132513>`
-  :ref:`References <cce_bulletin_0068__en-us_topic_0000001851583716_en-us_topic_0000001072975092_en-us_topic_0261805759_en-us_topic_0261793154_section1272182810583>`

.. _cce_bulletin_0068__en-us_topic_0000001851583716_section611420132265:

Important Notes
---------------

-  In Kubernetes 1.28, the scheduling framework is improved to reduce useless retries. The overall scheduling performance is enhanced. If a custom scheduler plugin is used in a cluster, you can perform the adaptation upgrade following instructions in `GitHub <https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.28.md#no-really-you-must-read-this-before-you-upgrade>`__.
-  The Ceph FS in-tree plugin has been deprecated in Kubernetes 1.28 and will be removed in Kubernetes 1.31. (The community does not plan to support CSI migration.) Use `Ceph CSI driver <https://github.com/ceph/ceph-csi>`__ instead.
-  The Ceph RBD in-tree plugin has been deprecated in Kubernetes 1.28 and will be removed in Kubernetes 1.31. (The community does not plan to support CSI migration.) Use RBD `Ceph CSI driver <https://github.com/ceph/ceph-csi>`__ instead.

.. _cce_bulletin_0068__en-us_topic_0000001851583716_section14972102702312:

New and Enhanced Features
-------------------------

Features in alpha stage are disabled by default, those in beta stage are enabled by default, and those in General Availability (GA) stage are always enabled and they cannot be disabled. The function of turning on or off the features in GA stage will be removed in later Kubernetes versions. CCE policies for new features are the same as those in the community.

-  The version skew policy is expanded to three versions.

   Starting with control planes 1.28 and worker nodes 1.25, the Kubernetes skew policy expands the supported control plane and worker node skew to three versions. This enables annual minor version upgrades of nodes while staying on supported minor versions. For details, see `Version Skew Policy <https://kubernetes.io/releases/version-skew-policy/>`__.

-  Retroactive Default StorageClass moves to GA.

   The retroactive default StorageClass assignment graduates to GA. This enhancement brings a significant improvement to how default StorageClasses are assigned to PersistentVolumeClaims (PVCs).

   The PersistentVolume (PV) controller has been modified to automatically assign a default StorageClass to any unbound PVC with **storageClassName** not configured. Additionally, the PVC admission validation mechanism within the API server has been adjusted to allow changing values from an unset state to an actual StorageClass name. For details, see `Retroactive default StorageClass assignment <https://kubernetes.io/docs/concepts/storage/persistent-volumes/#retroactive-default-storageclass-assignment>`__.

-  Native sidecar containers are introduced.

   The native sidecar containers are available in alpha. Kubernetes 1.28 adds **restartPolicy** to Init containers. This field is available when the SidecarContainers feature gate is enabled. However, there are still some problems to be solved in the native sidecar containers. Therefore, the Kubernetes community recommends only using this feature gate in `short lived testing clusters <https://kubernetes.io/docs/reference/command-line-tools-reference/feature-gates/#feature-stages>`__ at the alpha phase. For details, see `Introducing native sidecar containers <https://kubernetes.io/blog/2023/08/25/native-sidecar-containers/>`__.

-  Mixed version proxy is introduced.

   A new mechanism (mixed version proxy) is released to improve cluster upgrade. It is an alpha feature in Kubernetes 1.28. When a cluster undergoes an upgrade, API servers of different versions in the cluster can serve different sets (groups, versions, or resources) of built-in resources. A resource request made in this scenario may be served by any of the available API servers, potentially resulting in the request ending up at an API server that may not be aware of the requested resource. As a result, the request fails. This feature can solve this problem. (Note that CCE provides hitless upgrade. Therefore, this feature is not used in CCE clusters.) For details, see `A New (alpha) Mechanism For Safer Cluster Upgrades <https://kubernetes.io/blog/2023/08/28/kubernetes-1-28-feature-mixed-version-proxy-alpha/>`__.

-  Non-graceful node shutdown moves to GA.

   The non-graceful node shutdown is now GA in Kubernetes 1.28. When a node was shut down and that shutdown was not detected by the Kubelet's Node Shutdown Manager, the StatefulSet pods that run on this node will stay in the terminated state and cannot be moved to a running node. If you have confirmed that the shutdown node is unrecoverable, you can add an **out-of-service** taint to the node. This ensures that the StatefulSet pods and VolumeAttachments on this node can be forcibly deleted and the corresponding pods will be created on a healthy node. For details, see `Non-Graceful Node Shutdown Moves to GA <https://kubernetes.io/blog/2023/08/16/kubernetes-1-28-non-graceful-node-shutdown-ga/>`__.

-  NodeSwap moves to beta.

   Support for NodeSwap goes to beta in Kubernetes 1.28. NodeSwap is disabled by default and can be enabled using the NodeSwap feature gate. NodeSwap allows you to configure swap memory usage for Kubernetes workloads running on Linux on a per-node basis. Note that although NodeSwap has reached beta, there are still some problems to be solved and security risks to be enhanced. For details, see `Beta Support for Using Swap on Linux <https://kubernetes.io/blog/2023/08/24/swap-linux-beta/>`__.

-  Two Job-related features are added.

   Two alpha features are introduced: `Delayed creation of replacement pods <https://kubernetes.io/docs/concepts/workloads/controllers/job/#pod-replacement-policy>`__ and `Backoff limit per index <https://kubernetes.io/docs/concepts/workloads/controllers/job/#backoff-limit-per-index>`__.

   -  Delayed creation of replacement pods

      By default, when a pod enters the terminating state (for example, due to the preemption or eviction), Kubernetes immediately creates a replacement pod. Therefore, both pods are running concurrently.

      In Kubernetes 1.28, this feature can be enabled by turning on the JobPodReplacementPolicy feature gate. With this feature gate enabled, you can set the **podReplacementPolicy** field under **spec** of a Job to **Failed**. In this way, pods would only be replaced when they reached the failed phase, and not when they are terminating. Additionally, you can check the **.status.termination** field of a Job. The value of this field is the number of pods owned by the Job that are currently terminating.

   -  Backoff limit per index

      By default, pod failures for indexed Jobs are counted and restricted by the global limit of retries, specified by **.spec.backoffLimit**. This means that if there is a consistently failing index in a Job, pods specified by the Job will be restarted repeatedly until pod failures exhaust the limit. Once the limit is reached, the Job is marked failed and pods for other indexes in the Job may never be even started. In this case, the backoff limit per index configuration is useful.

      In Kubernetes 1.28, this feature can be enabled by turning on the JobBackoffLimitPerIndex feature gate of a cluster. With this feature gate enabled, **.spec.backoffLimitPerIndex** can be specified when an indexed Job is created. Only if the failures of pods with all indexes specified in this Job exceed the upper limit, pods specified by the Job will not be restarted.

-  Some Common Expression Language (CEL) related features are improved.

   CEL related capabilities are enhanced.

   -  CEL used to validate CustomResourceDefinitions (CRDs) moves to beta.

      This feature has been upgraded to beta since Kubernetes 1.25. By embedding CEL expressions into CRDs, developers can solve most of the CR validation use cases without using webhooks. More CEL functions, such as support for default value and CRD conversion, will be developed in later Kubernetes versions.

   -  CEL admission control graduates to beta.

      CEL admission control is customizable. With CEL expressions, you can decide whether to accept or reject requests received by kube-apiserver. CEL expressions can also serve as a substitute for admission webhooks. Kubernetes 1.28 has upgraded CEL admission control to beta and introduced new functions, such as:

      -  ValidatingAdmissionPolicy can correctly handle the **authorizer** variable.
      -  ValidatingAdmissionPolicy can have the **messageExpression** field checked.
      -  The ValidatingAdmissionPolicy controller is added to kube-controller-manager to check the type of the CEL expression in ValidatingAdmissionPolicy and save the reason in the **status** field.
      -  CEL expressions can contain a combination of one or more variables, which can be defined in ValidatingAdmissionPolicy. These variables can be used to define other variables.
      -  CEL library functions can be used to parse resources specified by **resource.Quantity** in Kubernetes.

-  Other features

   -  The ServiceNodePortStaticSubrange feature gate moves to beta. With this feature enabled, static port range can be reserved to avoid conflicts with dynamically allocated ports. For details, see `Avoiding Collisions Assigning Ports to NodePort Services <https://kubernetes.io/blog/2023/05/11/nodeport-dynamic-and-static-allocation/>`__.
   -  The alpha feature ConsistentListFromCache is added to allow the API server to serve consistent lists from cache. Get and list requests can read data from the cache instead of etcd.
   -  In Kubernetes 1.28, kubelet can configure the drop-in directory (alpha). This feature allows you to add support for the **--config-dir** flag to kubelet so that you can specify an insert directory that overwrites the kubelet configuration in **/etc/kubernetes/kubelet.conf**.
   -  ExpandedDNSConfig moves to GA and is enabled by default. With this feature enabled, DNS configurations can be expanded.
   -  The alpha feature CRDValidationRatcheting is added. This feature allows CRs with failing validations to pass if a Patch or Update request does not alter any of the invalid fields.
   -  **--concurrent-cron-job-syncs** is added to kube-controller-manager to configure the number of workers for the cron job controller.

.. _cce_bulletin_0068__en-us_topic_0000001851583716_section1898982110241:

API Changes and Removals
------------------------

-  **NetworkPolicyStatus** is removed. There is no status attribute in a network policy.
-  **annotationbatch.kubernetes.io/cronJob-scheduled-timestamp** is added to Job objects to indicate the creation time of a Job.
-  The **podReplacementPolicy** and **terminating** fields are added to Job APIs. With these fields specified, once a previously created pod is terminated in a Job, the Job immediately starts a new pod to replace the pod. The new fields allow you to specify whether to replace the pod immediately after the previous pod is terminated (original behavior) or replace the pod after the existing pod is completely terminated (new behavior). This is an alpha feature, and you can enable it by turning on the `JobPodReplacementPolicy <https://kubernetes.io/blog/2023/08/21/kubernetes-1-28-jobapi-update/>`__ feature gate in your cluster.
-  The **BackoffLimitPerIndex** field is available in a Job. Pods specified by a Job share a backoff mechanism. When backoff times of the Job reach the limit, this Job is marked as failed and resources, including indexes that are not running, are cleared up. This field allows you to configure backoff limit for a single index. For details, see `Backoff limit per index <https://kubernetes.io/docs/concepts/workloads/controllers/job/#backoff-limit-per-index>`__.
-  The **ServedVersions** field is added to the **StorageVersion** API. This change is introduced by mixed version proxy. The new field is used to indicate a version that can be provided by the API server.
-  **SelfSubjectReview** is added to **authentication.k8s.io/v1**, and **kubectl auth whoami** goes to GA.
-  **LastPhaseTransitionTime** is added to **PersistentVolume**. The new field is used to store the last time when a volume changes to a different phase.
-  **resizeStatus** in **PVC.Status** is replaced by **AllocatedResourceStatus**. The new field indicates the statuses of the storage resize operation. The default value is an empty string.
-  If **hostNetwork** is set to **true** and ports are specified for a pod, the **hostport** field will be automatically configured.
-  StatefulSet pods have the pod index set as a pod label **statefulset.kubernetes.io/pod-index**.
-  **PodHasNetwork** in the **Condition** field of pods has been renamed to **PodReadyToStartContainers**. The new field specifies that containers are ready to start after the network, volumes, and sandbox pod have been created.
-  A new configuration option **delayCacheUntilActive** is added to **KubeSchedulerConfiguration**. If **delayCacheUntilActive** is set to **true**, kube-scheduler on the leader will not cache scheduling information. This reduces the memory pressure of other master nodes, but slows down the failover speed after the leader failed.
-  The **namespaceParamRef** field is added to **admissionregistration.k8s.io/v1alpha1.ValidatingAdmissionPolicy**.
-  The **reason** and **fieldPath** fields are added to CRD validation rules to allow you to specify reason and field path after verification failed.
-  The CEL expression of ValidatingAdmissionPolicy supports namespace access via namespaceObject.
-  API groups ValidatingAdmissionPolicy and ValidatingAdmissionPolicyBinding are promoted to betav1.
-  A ValidatingAdmissionPolicy now has its **messageExpression** field checked against resolved types.

.. _cce_bulletin_0068__en-us_topic_0000001851583716_section7619242123210:

Feature Gate and Command Line Parameter Changes and Removals
------------------------------------------------------------

-  **-short** is removed from kubelet. Therefore, the default output of **kubectl version** is the same as that of **kubectl version -short**.
-  **--volume-host-cidr-denylist** and **--volume-host-allow-local-loopback** are removed from kube-controller-manager. **--volume-host-cidr-denylist** is a comma-separated list of CIDR ranges. Volume plugins at these IP addresses are not allowed. If **--volume-host-allow-local-loopback** is set to **false**, the local loopback IP address and the CIDR ranges specified in **--volume-host-cidr-denylist** are disabled.
-  **--azure-container-registry-config** is deprecated in kubelet and will be deleted in later Kubernetes versions. Use **--image-credential-provider-config** and **--image-credential-provider-bin-dir** instead.
-  **--lock-object-namespace** and **--lock-object-name** are removed from kube-scheduler. Use **--leader-elect-resource-namespace** and **--leader-elect-resource-name** or **ComponentConfig** instead. (**--lock-object-namespace** is used to define the namespace of a lock object, and **--lock-object-name** is used to define the name of a lock object.)
-  KMS v1 is deprecated and will only receive security updates. Use KMS v2 instead. In later Kubernetes versions, use **--feature-gates=KMSv1=true** to configure a KMS v1 provider.
-  The DelegateFSGroupToCSIDriver, DevicePlugins, KubeletCredentialProviders, MixedProtocolLBService, ServiceInternalTrafficPolicy, ServiceIPStaticSubrange, and EndpointSliceTerminatingCondition feature gates are removed.

.. _cce_bulletin_0068__en-us_topic_0000001851583716_section115291322132513:

Enhanced Kubernetes 1.28 on CCE
-------------------------------

During a version maintenance period, CCE periodically updates Kubernetes 1.28 and provides enhanced functions.

For details about cluster version updates, see :ref:`Release Notes for CCE Cluster Versions <cce_10_0405>`.

.. _cce_bulletin_0068__en-us_topic_0000001851583716_en-us_topic_0000001072975092_en-us_topic_0261805759_en-us_topic_0261793154_section1272182810583:

References
----------

For more details about the performance comparison and function evolution between Kubernetes 1.28 and other versions, see `Kubernetes v1.28 Release Notes <https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.28.md>`__.
