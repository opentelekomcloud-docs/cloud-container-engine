:original_name: cce_bulletin_0058.html

.. _cce_bulletin_0058:

Kubernetes 1.25 Release Notes
=============================

CCE has passed the Certified Kubernetes Conformance Program and is a certified Kubernetes offering. This section describes the changes made in Kubernetes 1.25 compared with Kubernetes 1.23.

Indexes
-------

-  :ref:`New Features <cce_bulletin_0058__en-us_topic_0000001851742752_en-us_topic_0000001389397618_en-us_topic_0000001430891141_en-us_topic_0000001072975092_section51381161799>`
-  :ref:`Deprecations and Removals <cce_bulletin_0058__en-us_topic_0000001851742752_section1096111394018>`
-  :ref:`Enhanced Kubernetes 1.25 on CCE <cce_bulletin_0058__en-us_topic_0000001851742752_section115291322132513>`
-  :ref:`References <cce_bulletin_0058__en-us_topic_0000001851742752_en-us_topic_0000001389397618_en-us_topic_0000001430891141_en-us_topic_0000001072975092_en-us_topic_0261805759_en-us_topic_0261793154_section1272182810583>`

.. _cce_bulletin_0058__en-us_topic_0000001851742752_en-us_topic_0000001389397618_en-us_topic_0000001430891141_en-us_topic_0000001072975092_section51381161799:

New Features
------------

**Kubernetes 1.25**

-  Pod Security Admission is stable. PodSecurityPolicy is deprecated.

   PodSecurityPolicy is replaced by Pod Security Admission. For details about the migration, see `Migrate from PodSecurityPolicy to the Built-In PodSecurity Admission Controller <https://kubernetes.io/docs/tasks/configure-pod-container/migrate-from-psp/>`__.

-  The ephemeral container is stable.

   An `ephemeral container <https://kubernetes.io/docs/concepts/workloads/pods/ephemeral-containers/>`__ is a container that runs temporarily in an existing pod. It is useful for troubleshooting, especially when kubectl exec cannot be used to check a container that breaks down or its image lacks a debugging tool.

-  Support for cgroups v2 enters the stable phase.

   Kubernetes supports cgroups v2. cgroups v2 provides some improvements over cgroup v1. For details, see `About cgroup v2 <https://kubernetes.io/docs/concepts/architecture/cgroups/>`__.

-  SeccompDefault moves to beta.

   To enable this feature, add the startup parameter **--seccomp-default=true** to kubelet. In this way, **seccomp** is set to **RuntimeDefault** by default, improving system security. Clusters of v1.25 no longer support **seccomp.security.alpha.kubernetes.io/pod** and **container.seccomp.security.alpha.kubernetes.io/annotation**. Replace them with the **securityContext.seccompProfile** field in pods or containers. For details, see `Configure a Security Context for a Pod or Container <https://kubernetes.io/docs/tasks/configure-pod-container/security-context/>`__.

   .. note::

      After this feature is enabled, the system calls required by the application may be restricted by the runtime. Ensure that the debugging is performed in the test environment, so that application is not affected.

-  The EndPort in the network policy moves to stable.

   EndPort in Network Policy is stable. This feature is incorporated in version 1.21. EndPort is added to NetworkPolicy. You can specify a port range.

-  Local ephemeral storage capacity isolation is stable.

   This feature provides support for capacity isolation of local ephemeral storage between pods, such as EmptyDir. If a pod's consumption of shared resources exceeds the limit, it will be evicted.

-  The CRD verification expression language moves to beta.

   This makes it possible to declare how to validate custom resources using `Common Expression Language (CEL) <https://github.com/google/cel-spec>`__. For details, see `Extend the Kubernetes API with CustomResourceDefinitions <https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/>`__.

-  KMS v2 APIs are introduced.

   The KMS v2 alpha1 API is introduced to add performance, rotation, and observability improvements. This API uses AES-GCM to replace AES-CBC and uses DEK to encrypt data at rest (Kubernetes Secrets). No additional operation is required during this process. Additionally, data can be read through AES-GCM and AES-CBC. For details, see `Using a KMS provider for data encryption <https://kubernetes.io/docs/tasks/administer-cluster/kms-provider/>`__.

-  Pod network readiness is introduced.

   Kubernetes 1.25 introduces Alpha support for PodHasNetwork. This status is in the **status** field of the pod. For details, see `Pod network readiness <https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#pod-has-network>`__.

-  The two features used for application rollout are stable.

   -  In Kubernetes 1.25, **minReadySeconds** for StatefulSets is stable. It allows each pod to wait for an expected period of time to slow down the rollout of a StatefulSet. For details, see `Minimum ready seconds <https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/#minimum-ready-seconds>`__.
   -  In Kubernetes 1.25, **maxSurge** for DaemonSets is stable. It allows a DaemonSet workload to run multiple instances of the same pod on one node during a rollout. This minimizes DaemonSet downtime for users. DaemonSet does not allow **maxSurge** and **hostPort** to be used at the same time because two active pods cannot share the same port on the same node. For details, see `Perform a Rolling Update on a DaemonSet <https://kubernetes.io/docs/tasks/manage-daemon/update-daemon-set/>`__.

-  Alpha support for running pods with user namespaces is provided.

   This feature maps the **root** user in a pod to a non-zero ID outside the container. In this way, the container runs as the **root** user and the node runs as a regular unprivileged user. This feature is still in the internal test phase. The UserNamespacesStatelessPodsSupport gate needs to be enabled, and the container runtime must support this function. For details, see `Kubernetes 1.25: alpha support for running Pods with user namespaces <https://kubernetes.io/blog/2022/10/03/userns-alpha/>`__.

**Kubernetes 1.24**

-  Dockershim is removed from kubelet.

   Dockershim was marked deprecated in Kubernetes 1.20 and officially removed from kubelet in Kubernetes 1.24. If you want to use Docker container, switch to cri-dockerd or other runtimes that support CRI, such as containerd and CRI-O.

   .. note::

      Check whether there are agents or applications that depend on Docker Engine. For example, if **docker ps**, **docker run**, and **docker inspect** are used, ensure that multiple runtimes are compatible and switch to the standard CRI.

-  Beta APIs are disabled by default.

   The Kubernetes community found 90% cluster administrators did not care about the beta APIs and left them enabled. However, the beta features are not recommended because these APIs enabled in the production environment by default incur risks. Therefore, in 1.24 and later versions, beta APIs are disabled by default, but the existing beta APIs will retain the original settings.

-  OpenAPI v3 is supported.

   In Kubernetes 1.24 and later versions, OpenAPI V3 is enabled by default.

-  Storage capacity tracking is stable.

   In Kubernetes 1.24 and later versions, the CSIStorageCapacity API supports exposing the available storage capacity. This ensures that pods are scheduled to nodes with sufficient storage capacity, which reduces pod scheduling delay caused by volume creation and mounting failures. For details, see `Storage Capacity <https://kubernetes.io/docs/concepts/storage/storage-capacity/>`__.

-  gRPC container probe moves to beta.

   In Kubernetes 1.24 and later versions, the gRPC probe goes to beta. The feature gate GRPCContainerProbe is available by default. For details about how to use this probe, see `Configure Probes <https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/#configure-probes>`__.

-  LegacyServiceAccountTokenNoAutoGeneration is enabled by default.

   LegacyServiceAccountTokenNoAutoGeneration moves to beta. By default, this feature is enabled, where no secret token is automatically generated for a service account. To use a token that never expires, create a secret to hold the token. For details, see `Service account token Secrets <https://kubernetes.io/docs/concepts/configuration/secret/#service-account-token-secrets>`__.

-  IP address conflict is prevented.

   In Kubernetes 1.24, `an IP address pool is soft reserved for the static IP addresses of Services <https://kubernetes.io/docs/concepts/services-networking/service/#service-ip-static-sub-range>`__. After you manually enable this function, Service IP addresses will be automatically from the IP address pool to minimize IP address conflict.

-  Clusters are compiled based on Go 1.18.

   Kubernetes clusters of versions later than 1.24 are compiled based on Go 1.18. By default, the SHA-1 hash algorithm, such as SHA1WithRSA and ECDSAWithSHA1, is no longer supported for certificate signature verification. Use the certificate generated by the SHA256 algorithm instead.

-  The maximum number of unavailable StatefulSet replicas is configurable.

   In Kubernetes 1.24 and later versions, the **maxUnavailable** parameter can be configured for StatefulSets so that pods can be stopped more quickly during a rolling update.

-  Alpha support for non-graceful node shutdown is introduced.

   The non-graceful node shutdown is introduced as alpha in Kubernetes v1.24. A node shutdown is considered graceful only if kubelet's node shutdown manager can detect the upcoming node shutdown action. For details, see `Non-graceful node shutdown handling <https://kubernetes.io/docs/concepts/architecture/nodes/#non-graceful-node-shutdown>`__.

.. _cce_bulletin_0058__en-us_topic_0000001851742752_section1096111394018:

Deprecations and Removals
-------------------------

**Kubernetes 1.25**

-  The iptables chain ownership is cleared up.

   Kubernetes typically creates iptables chains to ensure data packets can be sent to the destination. These iptables chains and their names are for internal use only. These chains were never intended to be part of any Kubernetes API/ABI guarantees. For details, see `Kubernetes's IPTables Chains Are Not API <https://kubernetes.io/blog/2022/09/07/iptables-chains-not-api/>`__.

   In versions later than Kubernetes 1.25, Kubelet uses IPTablesCleanup to migrate the Kubernetes-generated iptables chains used by the components outside of Kubernetes in phases so that iptables chains such as KUBE-MARK-DROP, KUBE-MARK-MASQ, and KUBE-POSTROUTING will not be created in the NAT table. For more details, see `Cleaning Up IPTables Chain Ownership <https://github.com/kubernetes/enhancements/issues/3178>`__.

-  In-tree volume drivers from cloud service vendors are removed.

**Kubernetes 1.24**

-  In Kubernetes 1.24 and later versions, Service.Spec.LoadBalancerIP is deprecated because it cannot be used for dual-stack protocols. Instead, use custom annotations.
-  In Kubernetes 1.24 and later versions, the **--address**, **--insecure-bind-address**, **--port**, and **--insecure-port=0** parameters are removed from **kube-apiserver**.
-  In Kubernetes 1.24 and later versions, startup parameters **--port=0** and **--address** are removed from **kube-controller-manager** and **kube-scheduler**.
-  In Kubernetes 1.24 and later versions, **kube-apiserver --audit-log-version** and **--audit-webhook-version** support only **audit.k8s.io/v1**. In Kubernetes 1.24, **audit.k8s.io/v1[alpha|beta]1** is removed, and only **audit.k8s.io/v1** can be used.
-  In Kubernetes 1.24 and later versions, the startup parameter **--network-plugin** is removed from kubelet. This Docker-specific parameter is available only when the container runtime environment is **Docker** and it is deleted with Dockershim.
-  In Kubernetes 1.24 and later versions, dynamic log clearance has been discarded and removed accordingly. A log filter is introduced to the logs of all Kubernetes system components to prevent sensitive information from being leaked through logs. However, this function may block logs and therefore is discarded. For more details, see `Dynamic log sanitization <https://github.com/kubernetes/kubernetes/pull/107207>`__ and `KEP-1753 <https://github.com/kubernetes/enhancements/tree/master/keps/sig-instrumentation/1753-logs-sanitization#deprecation>`__.
-  VolumeSnapshot v1beta1 CRD is discarded in Kubernetes 1.20 and removed in Kubernetes 1.24. Use VolumeSnapshot v1 instead.
-  In Kubernetes 1.24 and later versions, **service annotation tolerate-unready-endpoints** discarded in Kubernetes 1.11 is replaced by **Service.spec.publishNotReadyAddresses**.
-  In Kubernetes 1.24 and later versions, the **metadata.clusterName** field is discarded and will be deleted in the next version.
-  In Kubernetes 1.24 and later versions, the logic for kube-proxy to listen to NodePorts is removed. If NodePorts conflict with **kernel net.ipv4.ip_local_port_range**, TCP connections may fail occasionally, which leads to a health check failure or service exception. Before the upgrade, ensure that cluster NodePorts do not conflict with **net.ipv4.ip_local_port_range** of all nodes in the cluster. For more details, see `Kubernetes PR <https://github.com/kubernetes/kubernetes/pull/108496>`__.

.. _cce_bulletin_0058__en-us_topic_0000001851742752_section115291322132513:

Enhanced Kubernetes 1.25 on CCE
-------------------------------

During a version maintenance period, CCE periodically updates Kubernetes 1.25 and provides enhanced functions.

For details about cluster version updates, see :ref:`Release Notes for CCE Cluster Versions <cce_10_0405>`.

.. _cce_bulletin_0058__en-us_topic_0000001851742752_en-us_topic_0000001389397618_en-us_topic_0000001430891141_en-us_topic_0000001072975092_en-us_topic_0261805759_en-us_topic_0261793154_section1272182810583:

References
----------

For more details about the performance comparison and function evolution between Kubernetes 1.25 and other versions, see the following documents:

-  `Kubernetes v1.25 Release Notes <https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.25.md>`__
-  `Kubernetes v1.24 Release Notes <https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.24.md>`__
