:original_name: cce_bulletin_0116.html

.. _cce_bulletin_0116:

containerd 2.x Release Notes
================================

From CCE clusters v1.35, the container runtime is upgraded to containerd 2.x by default. Compared with containerd 1.x, containerd 2.x has significant improvements in image security, runtime performance, and multi-sandbox support.

This section describes the core changes (including new features and deprecated functions) in containerd 2.x, as well as precautions during the upgrade and a self-check guide.

Functions and Core Changes
----------------------------

The following describes only the main features. For details, see `containerd Release Notes <https://github.com/containerd/containerd/blob/main/docs/containerd-2.0.md>`__.

New/Enhanced Features
----------------------

containerd features:

- **NRI enabled by default**: This feature allows external plugins to participate in the container lifecycle for more refined management and allocation of node resources.
- **CDI enabled by default**: This feature standardizes the way that complex devices (such as GPUs and FPGAs) are injected into containers.
- **Sandbox APIs**: PodSandbox is supported. This feature provides better underlying architecture support for containers running at multiple isolation levels (such as Kata Containers or other lightweight security sandboxes).

Kubernetes upstream features:

- `User namespaces <https://kubernetes.io/docs/concepts/workloads/pods/user-namespaces/>`__: The underlying isolation technology allows the root user in a pod to run as a non-privileged user on the host. This greatly improves the security isolation of containers. In addition, Kubernetes relaxes the security restrictions (such as the runAsNonRoot check) on such pods. Note that this feature depends on the Linux 6.3+ kernel. Ensure that the kernel supports this feature. HCE 3.0 is recommended.
- `Image volumes <https://kubernetes.io/docs/tasks/configure-pod-container/image-volumes/>`__: The content from an OCI image can be directly mounted as a data volume to a pod. With the support of containerd, you can reuse the distribution capability of image registries to deliver static configurations, model files, or build products without running them in independent containers. (Note: It is possible to utilize subPath from Kubernetes 1.33 when using the image volume feature.)

Deprecated Functions and Removed APIs
--------------------------------------

- **Removal of the CRI v1alpha2 API**: This API was deprecated from Kubernetes 1.26 and has been removed from containerd 2.x. If you have custom components that depend on this API, use CRI v1 instead.
- **Deprecation of Docker Schema 1 images**: Pulling images in formats such as application/vnd.docker.distribution.manifest.v1+json will be rejected.
- **Stricter seccomp policy**: The io_uring_* system calls have been removed from the default seccomp profile. By default, containers are no longer allowed to initiate io_uring-related system calls.
- **Removal of AUFS snapshotter**: The AUFS storage driver is no longer supported.
- **Disuse of some configuration items**: The registry.auths, registry.configs, registry.mirrors, and [plugins."io.containerd.internal.v1".tracing] configurations have been removed. To implement the same functionality, you are advised to specify custom containerd parameters in the node pools on the console.

Pre-upgrade Check (Important)
------------------------------

Before the upgrade to containerd 2.x, the system checks for deprecated APIs and incompatible items. If any obstacles are detected, the upgrade will be interrupted. You are advised to manually perform a pre-upgrade check before a cluster upgrade.

**Checking Whether the containerd Directory Is Directly Mounted**

If the containerd socket or related running directory is directly mounted to a service pod, the pod may be affected. You can run the following command to check the cluster (install the jq tool in advance by running the **yum install jq** command):

.. code-block::

    kubectl get pods --all-namespaces -o json | \
    jq -r '.items[] |
        select(.spec.volumes[]?.hostPath.path as $p |
          ["/", "/var", "/var/","/var/run", "/var/run/",
           "/var/run/containerd", "/var/run/containerd/",
           "/var/run/containerd/containerd.sock",
           "/run", "/run/", "/run/containerd", "/run/containerd/",
           "/run/containerd/containerd.sock"] | index($p)) |
        .metadata.namespace + "/" + .metadata.name'

**Identifying Deprecated API Calls on a Node**

Log in to the node and use the ctr command line tool to check whether any deprecated API is being called:

.. code-block::

    ctr deprecations list

**Checking for Docker Schema 1 Images**

Run the following command on the node to check whether there are Docker Schema 1 images in the local cache:

.. code-block::

    ctr --namespace k8s.io images list 'labels."io.containerd.image/converted-docker-schema1"'

Upgrade Method
----------------

Upgrade the cluster to v1.35 or later. During the upgrade, containerd will be automatically upgraded to 2.x. For details, see :ref:`Upgrading a Cluster <cce_10_0197>`.

FAQs
-----------------

- **Will services be affected during the containerd upgrade?**

By default, an in-place upgrade is performed. During the upgrade, the containers do not restart, and services are not affected.

- **Can I roll back the containerd upgrade from 1.7 to 2.x if I encounter any issues?**

Rollback is not supported. containerd 2.x introduces the Shim v3 API, which is incompatible with the Shim v2 API used in containerd 1.x. Once the upgrade is complete, the system cannot be downgraded to the source version.

- **Will container data (such as images and container logs) on the nodes be lost during the upgrade?**

No. During the in-place upgrade, containerd 2.x will continue to use the original data directories (Data Root and State Root), and the existing data will not be lost.

Helpful Links
-----------------

- `containerd Community Release History <https://github.com/containerd/containerd/blob/v2.0.0/RELEASES.md>`__
- `containerd Community Change Description <https://github.com/containerd/containerd/blob/main/docs/containerd-2.0.md>`__