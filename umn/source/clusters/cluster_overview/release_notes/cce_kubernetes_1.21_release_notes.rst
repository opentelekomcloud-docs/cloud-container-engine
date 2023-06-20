:original_name: cce_10_0469.html

.. _cce_10_0469:

CCE Kubernetes 1.21 Release Notes
=================================

CCE has passed the Certified Kubernetes Conformance Program and is a certified Kubernetes offering. This section describes the updates in CCE Kubernetes 1.21.

Resource Changes and Deprecations
---------------------------------

**Kubernetes 1.21 Release Notes**

-  CronJob is now in the stable state, and the version number changes to batch/v1.
-  The immutable Secret and ConfigMap have now been upgraded to the stable state. A new immutable field is added to these objects to reject changes. The rejection protects clusters from accidental updates that may cause application outages. As these resources are immutable, kubelet does not monitor or poll for changes. This reduces the load of kube-apiserver and improves scalability and performance of your clusters. For more information, see `Immutable ConfigMaps <https://kubernetes.io/docs/concepts/configuration/configmap/#configmap-immutable>`__.
-  Graceful node shutdown has been upgraded to the test state. With this update, kubelet can detect that a node is shut down and gracefully terminate the pods on the node. Prior to this update, when the node was shut down, its pod did not follow the expected termination lifecycle, which caused workload problems. Now kubelet can use systemd to detect the systems that are about to be shut down and notify the running pods to terminate them gracefully.
-  For a pod with multiple containers, you can use **kubectl.kubernetes.io/** to pre-select containers.
-  PodSecurityPolicy is deprecated. For details, see https://kubernetes.io/blog/2021/04/06/podsecuritypolicy-deprecation-past-present-and-future/.
-  The `BoundServiceAccountTokenVolume <https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/#bound-service-account-token-volume>`__ feature has entered the beta test. This feature improves the token security of the service account and changes the method of mounting tokens to pods. Kubernetes clusters of v1.21 and later enable this approach by default.

**Kubernetes 1.20 Release Notes**

-  The API priority and fairness have reached the test state and are enabled by default. This allows kube-apiserver to classify incoming requests by priority. For more information, see `API Priority and Fairness <https://kubernetes.io/docs/concepts/cluster-administration/flow-control/>`__.
-  The bug of **exec probe timeouts** is fixed. Before this bug is fixed, the exec probe does not consider the **timeoutSeconds** field. Instead, the probe will run indefinitely, even beyond its configured deadline. It will stop until the result is returned. Now, if no value is specified, the default value is used, that is, one second. If the detection time exceeds one second, the application health check may fail. Update the **timeoutSeconds** field for the applications that use this feature during the upgrade. The repair provided by the newly introduced ExecProbeTimeout feature gating enables the cluster operator to restore the previous behavior, but this behavior will be locked and removed in later versions.
-  RuntimeClass enters the stable state. RuntimeClass provides a mechanism to support multiple runtimes in a cluster and expose information about the container runtime to the control plane.
-  kubectl debugging has reached the test state. kubectl debugging provides support for common debugging workflows.
-  Dockershim was marked as deprecated in Kubernetes 1.20. Currently, you can continue to use Docker in the cluster. This change is irrelevant to the container image used by clusters. You can still use Docker to build your images. For more information, see `Dockershim Deprecation FAQ <https://kubernetes.io/blog/2020/12/02/dockershim-faq/>`__.

References
----------

For more details about the performance comparison and function evolution between Kubernetes 1.21 and other versions, see the following documents:

-  `Kubernetes v1.21 Release Notes <https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.21.md>`__
-  `Kubernetes v1.20 Release Notes <https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.20.md>`__
