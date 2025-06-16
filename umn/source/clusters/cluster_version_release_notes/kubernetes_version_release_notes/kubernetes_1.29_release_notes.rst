:original_name: cce_bulletin_0089.html

.. _cce_bulletin_0089:

Kubernetes 1.29 Release Notes
=============================

CCE allows you to create Kubernetes clusters 1.29. This section describes the changes made in Kubernetes 1.29.

Indexes
-------

-  :ref:`New and Enhanced Features <cce_bulletin_0089__en-us_topic_0000001802449514_section14972102702312>`
-  :ref:`API Changes and Removals <cce_bulletin_0089__en-us_topic_0000001802449514_section1898982110241>`
-  :ref:`Enhanced Kubernetes 1.29 on CCE <cce_bulletin_0089__en-us_topic_0000001802449514_section115291322132513>`
-  :ref:`References <cce_bulletin_0089__en-us_topic_0000001802449514_en-us_topic_0000001072975092_en-us_topic_0261805759_en-us_topic_0261793154_section1272182810583>`

.. _cce_bulletin_0089__en-us_topic_0000001802449514_section14972102702312:

New and Enhanced Features
-------------------------

-  The load balancer IP mode for Services is in the alpha state.

   The load balancer IP mode for Services is promoted to alpha. Kubernetes 1.29 adds the **ipMode** field to the Services' **status** field for configuring traffic forwarding from Services within a cluster to pods. If **ipMode** is set to **VIP**, traffic delivered to a node with the destination set to the load balancer's IP address and port will be redirected to the target node by kube-proxy. If it is set to **Proxy**, traffic delivered to a node will be sent to the load balancer and then redirected to the target node by the load balancer. This feature addresses the issue of load balancer functions being missed due to traffic bypassing it. For details, see `Load Balancer IP Mode for Services <https://kubernetes.io/blog/2023/12/18/kubernetes-1-29-feature-loadbalancer-ip-mode-alpha/>`__.

-  The nftables proxy mode is in the alpha state.

   The nftables proxy mode is promoted to alpha. This feature allows kube-proxy to run in nftables mode. In this mode, kube-proxy configures packet forwarding rules using the nftables API of the kernel netfilter subsystem. For details, see `nftables proxy mode <https://kubernetes.io/docs/reference/networking/virtual-ips/#proxy-mode-nftables>`__.

-  Garbage collection for unused container images is in the alpha state.

   The garbage collection for unused container images is promoted to alpha. This feature allows you to specify the maximum time a local image can be unused for each node. If the time expires, the image will be garbage collected. To configure the setting, specify the **ImageMaximumGCAge** field for kubelet. For details, see `Garbage collection for unused container images <https://kubernetes.io/docs/concepts/architecture/garbage-collection/#image-maximum-age-gc>`__.

-  **PodLifecycleSleepAction** is in the alpha state.

   **PodLifecycleSleepAction** is promoted to alpha. This feature introduces the sleep hook to the container lifecycle hooks. You can pause a container for a specified duration after it starts or before it is stopped by enabling this feature. For details, see `Hook handler implementations <https://kubernetes.io/docs/concepts/containers/container-lifecycle-hooks/#hook-handler-implementations>`__.

-  **KubeletSeparateDiskGC** is in the alpha state.

   **KubeletSeparateDiskGC** is promoted to alpha. With this feature enabled, container images and containers can be garbage collected even if they are on separate file systems.

-  **matchLabelKeys** and **mismatchLabelKeys** are in the alpha state.

   **matchLabelKeys** and **mismatchLabelKeys** are promoted to alpha. With these features enabled, the **matchLabelKeys** and **mismatchLabelKeys** fields are added to the pod affinity and anti-affinity configurations. This allows for configurations of more affinity and anti-affinity policies between pods. For details, see `matchLabelKeys and mismatchLabelKeys <https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#matchlabelkeys>`__.

-  The **clusterTrustBundle** projected volumes are in the alpha state.

   The **clusterTrustBundle** projected volumes are promoted to alpha. With this feature enabled, the **clusterTrustBundle** projected volume source injects the contents of one or more ClusterTrustBundle objects as an automatically-updating file. For details, see `clusterTrustBundle projected volumes <https://kubernetes.io/docs/concepts/storage/projected-volumes/#clustertrustbundle>`__.

-  Pulling images based on runtime classes of pods is in the alpha state.

   Pulling images based on runtime classes is promoted to alpha. With this feature enabled, the kubelet references container images by a tuple (of image name or runtime handler) rather than just the image name or digest. Your container runtime may adapt its behavior based on the selected runtime handler. Pulling images based on runtime classes will be helpful for VM based containers. For details, see `Image pull per runtime class <https://kubernetes.io/docs/concepts/containers/images/#image-pull-per-runtime-class>`__.

-  The **PodReadyToStartContainers** condition is in the beta state.

   The **PodReadyToStartContainers** condition is promoted to beta. Kubernetes 1.29 introduces the **PodReadyToStartContainers** condition to the pods' **status** field. If it is set to **true**, the sandbox of a pod is ready and service containers can be created. This feature enables cluster administrators to gain a clearer and more comprehensive view of pod sandbox creation completion and container readiness. This enhanced visibility allows them to make better-informed decisions and troubleshoot issues more effectively. For details, see `PodReadyToStartContainers Condition Moves to Beta <https://kubernetes.io/blog/2023/12/19/pod-ready-to-start-containers-condition-now-in-beta/>`__.

-  Two job-related features are in the beta state.

   -  Pod replacement policy (beta)

      The pod replacement policy feature moves to beta. This feature ensures that a pod is replaced only when it reaches the **Failed** state, which means that **status.phase** becomes **Failed**. It does not recreate a pod when the deletion timestamp is not empty and the pod is still being deleted. This prevents two pods from occupying index and node resources concurrently.

   -  Backoff limit per index (beta)

      The backoff limit per index moves to beta. By default, pod failures for indexed jobs are counted and restricted by the global limit of retries, specified by **.spec.backoffLimit**. This means that if there is a consistently failing index in a job, pods specified by the job will be restarted repeatedly until pod failures exhaust the limit. Once the limit is reached, the job is marked failed and pods for other indexes in the job may never be even started. The feature allows you to complete execution of all indexes, despite some indexes failing, and to better use the compute resources by avoiding unnecessary retries of consistently failing indexes.

-  Native sidecar containers are in the beta state.

   Native sidecar containers are promoted to beta. The **restartPolicy** field is added to **initContainers**. When this field is set to **Always**, the sidecar container is enabled. The sidecar container and service container are deployed in the same pod. This cannot prolong the pod lifecycle. Sidecar containers are commonly used in scenarios such as network proxy and log collection. For details, see `Sidecar Containers <https://kubernetes.io/docs/concepts/workloads/pods/sidecar-containers/>`__.

-  The legacy ServiceAccount token cleaner is in the beta state.

   Legacy ServiceAccount token cleaner is promoted to beta. It runs as part of **kube-controller-manager** and checks every 24 hours to see if any auto-generated legacy ServiceAccount token has not been used in a specific amount of time (one year by default, specified by **--legacy-service-account-token-clean-up-period**). If so, the cleaner marks those tokens as invalid and adds the **kubernetes.io/legacy-token-invalid-since** label whose value is the current date. If an invalid token is not used for a specific period of time (one year by default, specified by **--legacy-service-account-token-clean-up-period**), the cleaner deletes it. For details, see `Legacy ServiceAccount token cleaner <https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/#legacy-serviceaccount-token-cleaner>`__.

-  **DevicePluginCDIDevices** is in the beta state.

   **DevicePluginCDIDevices** moves to beta. With this feature enabled, plugin developers can use the **CDIDevices** field added to **DeviceRunContainerOptions** to pass CDI device names directly to CDI enabled runtimes.

-  **PodHostIPs** is in the beta state.

   The **PodHostIPs** feature moves to beta. With this feature enabled, Kubernetes adds the **hostIPs** field to **Status** of pods and downward API to expose node IP addresses to workloads. This field specifies the dual-stack protocol version of the host IP address. The first IP address is always the same as the host IP address.

-  The API Priority and Fairness (APF) feature is in the GA state.

   APF moves to GA. APF classifies and isolates requests in a more fine-grained way. It improves max-inflight limitations. It also introduces a limited amount of queuing, so that the API server does not reject any request in cases of very brief bursts. Requests are dispatched from queues using a fair queuing technique so that, for example, a poorly-behaved controller does not cause others (even at the same priority level) to become abnormal. For details, see `API Priority and Fairness <https://kubernetes.io/docs/concepts/cluster-administration/flow-control/>`__.

-  **APIListChunking** is in the GA state.

   The **APIListChunking** feature moves to GA. This feature allows clients to perform pagination in List requests to avoid performance problems caused by returning too much data at a time.

-  **ServiceNodePortStaticSubrange** is in the GA state.

   The **ServiceNodePortStaticSubrange** feature moves to GA. With this feature enabled, kubelet calculates the size of reserved IP addresses based on the ranges of the NodePort Services and divides node ports into static band and dynamic band. During automatic node port assignment, dynamic band is preferentially assigned, which helps avoid port conflicts during static band assignment. For details, see `ServiceNodePortStaticSubrange <https://kubernetes.io/blog/2023/05/11/nodeport-dynamic-and-static-allocation/>`__.

-  The phase transition timestamp of PersistentVolume (PV) is in the beta state.

   The PV phase transition timestamp moves to beta. With this feature enabled, Kubernetes adds the **lastPhaseTransitionTime** field to the **status** field of a PV to indicate the time when the PV phase changes last time. Cluster administrators are now able to track the last time a PV transitioned to a different phase, allowing for more efficient and informed resource management. For details, see `PersistentVolume Last Phase Transition Time in Kubernetes <https://kubernetes.io/blog/2023/10/23/persistent-volume-last-phase-transition-time/>`__.

-  **ReadWriteOncePod** is in the GA state.

   The **ReadWriteOncePod** feature moves to GA. With this feature enabled, you can set the access mode to **ReadWriteOncePod** in a PersistentVolumeClaim (PVC) to ensure that only one pod can modify data in the volume at a time. This can prevent data conflicts or damage. For details, see `ReadWriteOncePod <https://kubernetes.io/docs/tasks/administer-cluster/change-pv-access-mode-readwriteoncepod/>`__.

-  **CSINodeExpandSecret** is in the GA state.

   The **CSINodeExpandSecret** feature moves to GA. This feature allows secret authentication data to be passed to a CSI driver for use when a node is added.

-  The CEL-based CRD verification capability is in the GA state.

   The CEL-based CRD verification capability moves to GA. With this feature enabled, you are allowed to use the CEL to define validation rules in CRDs, which are more efficient than webhook. For details, see `CRD verification rules <https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/#validation-rules>`__.

.. _cce_bulletin_0089__en-us_topic_0000001802449514_section1898982110241:

API Changes and Removals
------------------------

-  The time zone of a newly created cron job cannot be configured using **TZ** or **CRON_TZ** in **.spec.schedule**. Use **.spec.timeZone** instead. Cron jobs that have been created are not affected by this change.
-  The alpha API **ClusterCIDR** is removed.
-  The startup parameter **--authentication-config** is added to kube-apiserver to specify the address of the **AuthenticationConfiguration** file. This startup parameter is mutually exclusive with the **--oidc-\*** startup parameter.
-  The API version **kubescheduler.config.k8s.io/v1beta3** of **KubeSchedulerConfiguration** is removed. Migrate **kube-scheduler** configuration files to **kubescheduler.config.k8s.io/v1**.
-  The CEL expressions are added to **v1alpha1 AuthenticationConfiguration**.
-  The **ServiceCIDR** type is added. It allows you to dynamically configure the IP address range used by a cluster to allocate the Service ClusterIPs.
-  The startup parameters **--conntrack-udp-timeout** and **--conntrack-udp-timeout-stream** are added to **kube-proxy**. They are options for configuring the kernel parameters **nf_conntrack_udp_timeout** and **nf_conntrack_udp_timeout_stream**.
-  Support for CEL expressions is added to **WebhookMatchCondition** of **v1alpha1 AuthenticationConfiguration**.
-  The type of **PVC.spec.Resource** is changed from **ResourceRequirements** to **VolumeResourceRequirements**.
-  **onPodConditions** in **PodFailurePolicyRule** is marked as optional.
-  The API version **flowcontrol.apiserver.k8s.io/v1beta3** of **FlowSchema** and **PriorityLevelConfiguration** has been promoted to **flowcontrol.apiserver.k8s.io/v1**, and the following changes have been made:

   -  **PriorityLevelConfiguration**: The **.spec.limited.nominalConcurrencyShares** field defaults to **30** if the field is omitted. To ensure compatibility with 1.28 API servers, specifying an explicit **0** value is not allowed in the **v1** version in 1.29. In 1.30, explicit **0** values will be allowed in this field in the **v1** API. The **flowcontrol.apiserver.k8s.io/v1beta3** APIs are deprecated and will no longer be served in 1.32.

-  The kube-proxy command line document is updated. kube-proxy does not bind any socket to the IP address specified by **--bind-address**.
-  The **selectorSpread** scheduler plugin is replaced by **podTopologySpread**.
-  If CSI-Node-Driver is not running, NodeStageVolume calls will be retried.
-  **ValidatingAdmissionPolicy** type checking now supports CRDs. To use this feature, the **ValidatingAdmissionPolicy** feature gate must be enabled.
-  The startup parameter **--nf-conntrack-tcp-be-liberal** is added to **kube-proxy**. You can configure it by setting the kernel parameter **nf_conntrack_tcp_be_liberal**.
-  The startup parameter **--init-only** is added to **kube-proxy**. Setting the flag makes **kube-proxy** init container run in the privileged mode, perform its initial configuration, and then exit.
-  The **fileSystem** field of container is added to the response body of CRI. It specifies the file system usage of a container. Originally, the **fileSystem** field contains only the file system of the container images.
-  All built-in cloud providers are disabled by default. If you still need to use them, you can configure the **DisableCloudProviders** and **DisableKubeletCloudCredentialProvider** feature gates to disable or enable cloud providers.
-  **--node-ips** can be used in kubelet to configure IPv4/IPv6 dual-stack. If **--cloud-provider** is set to **external**, you are allowed to use **--node-ips** to configure IPv4/IPv6 dual-stack for node IP addresses. To use **--node-ips**, you need to enable the **CloudDualStackNodeIPs** feature gate.

.. _cce_bulletin_0089__en-us_topic_0000001802449514_section115291322132513:

Enhanced Kubernetes 1.29 on CCE
-------------------------------

During a version maintenance period, CCE periodically updates Kubernetes 1.29 and provides enhanced functions.

For details about cluster version updates, see :ref:`Patch Versions <cce_10_0405>`.

.. _cce_bulletin_0089__en-us_topic_0000001802449514_en-us_topic_0000001072975092_en-us_topic_0261805759_en-us_topic_0261793154_section1272182810583:

References
----------

For more details about the performance comparison and function evolution between Kubernetes 1.29 and other versions, see `Kubernetes v1.29 Release Notes <https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.29.md>`__.
