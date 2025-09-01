:original_name: cce_bulletin_0095.html

.. _cce_bulletin_0095:

Kubernetes 1.30 Release Notes
=============================

CCE allows you to create Kubernetes 1.30 clusters. This section describes the changes made in Kubernetes 1.30.

Indexes
-------

-  :ref:`New and Enhanced Features <cce_bulletin_0095__en-us_topic_0000002011393057_section14972102702312>`
-  :ref:`API Changes and Removals <cce_bulletin_0095__en-us_topic_0000002011393057_section1898982110241>`
-  :ref:`Enhanced Kubernetes 1.30 on CCE <cce_bulletin_0095__en-us_topic_0000002011393057_section115291322132513>`
-  :ref:`References <cce_bulletin_0095__en-us_topic_0000002011393057_en-us_topic_0000001072975092_en-us_topic_0261805759_en-us_topic_0261793154_section1272182810583>`

.. _cce_bulletin_0095__en-us_topic_0000002011393057_section14972102702312:

New and Enhanced Features
-------------------------

-  Webhook matching expression is in the GA state.

   The Webhook matching expression feature is advanced to GA. This feature enables admission webhooks to be matched based on specific conditions, providing control over the triggering conditions of the webhooks in a more precise granularity. For details, see `Dynamic Admission Control <https://kubernetes.io/docs/reference/access-authn-authz/extensible-admission-controllers/#matching-requests-matchConditions>`__.

-  Pod scheduling readiness is in the GA state.

   The pod scheduling readiness feature is advanced to GA. With this feature, you can add custom scheduling gates to a pod and manage when to eliminate them. The pod will only be deemed ready for scheduling once all scheduling gates have been removed. For details, see `Pod Scheduling Readiness <https://kubernetes.io/docs/concepts/scheduling-eviction/pod-scheduling-readiness/>`__.

-  Validating admission policies are in the GA state.

   Validating admission policies are advanced to GA. This feature allows you to declare the validating admission policies of resources using Common Expression Language (CEL). For details, see `Validating Admission Policy <https://kubernetes.io/docs/reference/access-authn-authz/validating-admission-policy/>`__.

-  Horizontal pod auto scaling based on container resource metrics is in the GA state.

   The horizontal pod auto scaling feature based on container resource metrics is advanced to GA. This feature allows HPA to configure auto scaling based on the resource usage of each container within a pod, rather than just the overall resource usage of the pod. This makes it easier to set scaling thresholds for the most critical containers in a pod. For details, see `Container resource metrics <https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/#container-resource-metrics>`__.

-  The legacy ServiceAccount token cleaner is in the GA state.

   The legacy ServiceAccount token cleaner feature is advanced to GA. It runs as part of **kube-controller-manager** and checks every 24 hours to see if any auto-generated legacy ServiceAccount token has not been used in a specific amount of time (one year by default, specified by **--legacy-service-account-token-clean-up-period**). If so, the cleaner marks those tokens as invalid and adds the **kubernetes.io/legacy-token-invalid-since** label whose value is the current date. If an invalid token is not used for a specific period of time (one year by default, specified by **--legacy-service-account-token-clean-up-period**), the cleaner deletes it. For details, see `Legacy ServiceAccount token cleaner <https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/#legacy-serviceaccount-token-cleaner>`__.

-  The minimum domain in the pod topology spread is in the GA state.

   The minimum domain feature in pod topology spread is advanced to GA. This feature allows you to configure a minimum number of domains that meet specific conditions by using the **minDomains** field in the pod configuration. If the number of domains that match the load topology constraints exceeds the **minDomains** value, this field will not affect the settings. However, if the number of domains that match the load topology constraints is less than the **minDomains** value, the global minimum value is set to 0, which represents the minimum number of matched pods in domains that meet the conditions. To prevent pods from being scheduled when topology constraints are not met, this field must be used together with **whenUnsatisfiable: DoNotSchedule**. For details, see `Spread constraint definition <https://kubernetes.io/docs/concepts/scheduling-eviction/topology-spread-constraints/#spread-constraint-definition>`__.

.. _cce_bulletin_0095__en-us_topic_0000002011393057_section1898982110241:

API Changes and Removals
------------------------

-  kubectl removes the **prune-whitelist** parameter of the **apply** command and replaces it with **prune-allowlist**.
-  SecurityContextDeny, which has been deprecated in Kubernetes 1.27, is replaced by `Pod Security admission controller <https://kubernetes.io/docs/concepts/security/pod-security-admission/>`__.

.. _cce_bulletin_0095__en-us_topic_0000002011393057_section115291322132513:

Enhanced Kubernetes 1.30 on CCE
-------------------------------

During a version maintenance period, CCE periodically updates Kubernetes 1.30 and provides enhanced functions.

For details about cluster version updates, see :ref:`Patch Versions <cce_10_0405>`.

.. _cce_bulletin_0095__en-us_topic_0000002011393057_en-us_topic_0000001072975092_en-us_topic_0261805759_en-us_topic_0261793154_section1272182810583:

References
----------

For more details about the performance comparison and function evolution between Kubernetes 1.30 and other versions, see `Kubernetes v1.30 Release Notes <https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.30.md>`__.
