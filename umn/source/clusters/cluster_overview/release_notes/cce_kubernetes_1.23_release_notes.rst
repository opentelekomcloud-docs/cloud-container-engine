:original_name: cce_10_0468.html

.. _cce_10_0468:

CCE Kubernetes 1.23 Release Notes
=================================

CCE has passed the Certified Kubernetes Conformance Program and is a certified Kubernetes offering. This section describes the updates in CCE Kubernetes 1.23.

Resource Changes and Deprecations
---------------------------------

**Changes in CCE 1.23**

-  The web-terminal add-on is no longer supported. Use kubectl instead.

**Kubernetes 1.23 Release Notes**

-  FlexVolume is deprecated. Use CSI.
-  HorizontalPodAutoscaler v2 is promoted to GA, and HorizontalPodAutoscaler API v2 is gradually stable in version 1.23. The HorizontalPodAutoscaler v2beta2 API is not recommended. Use the v2 API.
-  `PodSecurity <https://kubernetes.io/docs/concepts/security/pod-security-admission/>`__ moves to beta, replacing the deprecated PodSecurityPolicy. PodSecurity is an admission controller that enforces pod security standards on pods in the namespace based on specific namespace labels that set the enforcement level. PodSecurity is enabled by default in version 1.23.

**Kubernetes 1.22 Release Notes**

-  Ingresses no longer support networking.k8s.io/v1beta1 and extensions/v1beta1 APIs. If you use the API of an earlier version to manage ingresses, an application cannot be exposed to external services. Use networking.k8s.io/v1.
-  CustomResourceDefinitions no longer support the apiextensions.k8s.io/v1beta1 API. If you use the API of an earlier version to create a CRD, the creation will fail, which affects the controller that reconciles this CRD. Use apiextensions.k8s.io/v1.
-  ClusterRoles, ClusterRoleBindings, Roles, and RoleBindings no longer support the rbac.authorization.k8s.io/v1beta1 API. If you use the API of an earlier version to manage RBAC resources, application permissions control is affected and even cannot work in the cluster. Use rbac.authorization.k8s.io/v1.
-  The Kubernetes release cycle is changed from four releases a year to three releases a year.
-  StatefulSets support **minReadySeconds**.
-  During scale-in, pods are randomly selected and deleted based on the pod UID by default (LogarithmicScaleDown). This feature enhances the randomness of the pods to be deleted and alleviates the problems caused by pod topology spread constraints. For more information, see `KEP-2185 <https://github.com/kubernetes/enhancements/tree/master/keps/sig-apps/2185-random-pod-select-on-replicaset-downscale>`__ and `issue 96748 <https://github.com/kubernetes/kubernetes/issues/96748?spm=a2c4g.11186623.0.0.1de23edc3yorUN>`__.
-  The `BoundServiceAccountTokenVolume <https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/#bound-service-account-token-volume>`__ feature is stable. This feature improves the token security of the service account and changes the method of mounting tokens to pods. Kubernetes clusters of v1.21 and later enable this approach by default.

References
----------

For more details about the performance comparison and function evolution between Kubernetes 1.23 and other versions, see the following documents:

-  `Kubernetes v1.23 Release Notes <https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.23.md>`__
-  `Kubernetes v1.22 Release Notes <https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.22.md>`__
