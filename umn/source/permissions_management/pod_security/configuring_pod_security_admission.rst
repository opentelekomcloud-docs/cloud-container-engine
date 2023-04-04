:original_name: cce_10_0466.html

.. _cce_10_0466:

Configuring Pod Security Admission
==================================

Before using `Pod Security Admission <https://kubernetes.io/docs/concepts/security/pod-security-admission/>`__, you need to understand Kubernetes `Pod Security Standards <https://kubernetes.io/docs/concepts/security/pod-security-standards/>`__. These standards define different isolation levels for pods. They let you define how you want to restrict the behavior of pods in a clear, consistent fashion. Kubernetes offers a built-in pod security admission controller to enforce the pod security standards. Pod security restrictions are applied at the namespace level when pods are created.

The pod security standard defines three security policy levels:

.. _cce_10_0466__table0547553318:

.. table:: **Table 1** Pod security policy levels

   +------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Level      | Description                                                                                                                                                                                                    |
   +============+================================================================================================================================================================================================================+
   | privileged | Unrestricted policy, providing the widest possible level of permissions, typically aimed at system- and infrastructure-level workloads managed by privileged, trusted users, such as CNIs and storage drivers. |
   +------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | baseline   | Minimally restrictive policy which prevents known privilege escalations, typically targeted at non-critical workloads. This policy disables capabilities such as hostNetwork and hostPID.                      |
   +------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | restricted | Heavily restricted policy, following current Pod hardening best practices.                                                                                                                                     |
   +------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

:ref:`Pod security admission <cce_10_0466__section4761636371>` is applied at the namespace level. The controller restricts the security context and other parameters in the pod or container in the namespace. The privileged policy does not verify the **securityContext** field of the pod and container. The baseline and restricted policies have different requirements on **securityContext**. For details, see `Pod Security Standards <https://kubernetes.io/docs/concepts/security/pod-security-standards/>`__.

Setting security context: `Configure a Security Context for a Pod or Container <https://kubernetes.io/docs/tasks/configure-pod-container/security-context/>`__

Pod Security Admission Labels
-----------------------------

Kubernetes defines three types of labels for Pod Security Admission (see :ref:`Table 2 <cce_10_0466__table198561415448>`). You can set these labels in a namespace to define the pod security standard level to be used. However, do not change the pod security standard level in system namespaces such as kube-system. Otherwise, pods in the system namespace may be faulty.

.. _cce_10_0466__table198561415448:

.. table:: **Table 2** Pod security admission labels

   +---------+----------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------+
   | Mode    | Target Object                          | Description                                                                                                                           |
   +=========+========================================+=======================================================================================================================================+
   | enforce | Pods                                   | Policy violations will cause the pod to be rejected.                                                                                  |
   +---------+----------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------+
   | audit   | Workloads (such as Deployment and job) | Policy violations will trigger the addition of an audit annotation to the event recorded in the audit log, but are otherwise allowed. |
   +---------+----------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------+
   | warn    | Workloads (such as Deployment and job) | Policy violations will trigger a user-facing warning, but are otherwise allowed.                                                      |
   +---------+----------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------+

.. note::

   Pods are often created indirectly, by creating a workload object such as a Deployment or job. To help catch violations early, both the audit and warning modes are applied to the workload resources. However, the enforce mode is applied only to the resulting pod objects.

.. _cce_10_0466__section4761636371:

Enforcing Pod Security Admission with Namespace Labels
------------------------------------------------------

You can label namespaces to enforce pod security standards. Assume that a namespace is configured as follows:

.. code-block::

   apiVersion: v1
   kind: Namespace
   metadata:
     name: my-baseline-namespace
     labels:
       pod-security.kubernetes.io/enforce: privileged
       pod-security.kubernetes.io/enforce-version: v1.25
       pod-security.kubernetes.io/audit: baseline
       pod-security.kubernetes.io/audit-version: v1.25
       pod-security.kubernetes.io/warn: restricted
       pod-security.kubernetes.io/warn-version: v1.25

       # The label can be in either of the following formats:
       # pod-security.kubernetes.io/<MODE>: <LEVEL>
       # pod-security.kubernetes.io/<MODE>-version: <VERSION>
       # The audit and warn modes inform you of which security behaviors are violated by the load.

Namespace labels indicate which policy level to apply for the mode. For each mode, there are two labels that determine the policy used:

-  pod-security.kubernetes.io/<MODE>: <LEVEL>

   -  <MODE>: must be **enforce**, **audit**, or **warn**. For details about the modes, see :ref:`Table 2 <cce_10_0466__table198561415448>`.
   -  <LEVEL>: must be **privileged**, **baseline**, or **restricted**. For details about the levels, see :ref:`Table 1 <cce_10_0466__table0547553318>`.

-  pod-security.kubernetes.io/<MODE>-version: <VERSION>

   Optional, which pins the policy to a given Kubernetes version.

   -  <MODE>: must be **enforce**, **audit**, or **warn**. For details about the modes, see :ref:`Table 2 <cce_10_0466__table198561415448>`.
   -  <VERSION>: Kubernetes version number. For example, v1.25. You can also use **latest**.

If pods are deployed in the preceding namespace, the following security restrictions apply:

#. The verification in the enforce mode is skipped (enforce mode + privileged level).
#. Restrictions related to the baseline policy are verified (audit mode + baseline level). That is, if the pod or container violates the policy, the corresponding event is recorded into the audit log.
#. Restrictions related to the restricted policy are verified (warn mode + restricted level). That is, if the pod or container violates the policy, the user will receive an alarm when creating the pod.

Migrating from Pod Security Policy to Pod Security Admission
------------------------------------------------------------

If you use pod security policies in a cluster earlier than v1.25 and need to replace them with pod security admission in a cluster of v1.25 or later, follow the guide in `Migrate from PodSecurityPolicy to the Built-In PodSecurity Admission Controller <https://kubernetes.io/docs/tasks/configure-pod-container/migrate-from-psp/#eliminating-mutaging-fields>`__.

.. important::

   #. Pod security admission supports only three isolation modes, less flexible than pod security policies. If you require more control over specific constraints, you will need to use a Validating Admission Webhook to enforce those policies.
   #. Pod security admission is a non-mutating admission controller, meaning it will not modify pods before validating them. If you were relying on this aspect of PSP, you will need to either modify the security context in your workloads, or use a Mutating Admission Webhook to make those changes.
   #. PSP lets you bind different policies to different service accounts. This approach has many pitfalls and is not recommended, but if you require this feature anyway you will need to use a third-party webhook instead.
   #. Do not apply pod security admission to namespaces where CCE components, such as kube-system, kube-public, and kube-node-lease, are deployed. Otherwise, CCE components and add-on functions will be abnormal.

Reference
---------

-  `Pod Security Admission <https://kubernetes.io/docs/concepts/security/pod-security-admission/>`__
-  `Mapping PodSecurityPolicies to Pod Security Standards <https://kubernetes.io/docs/reference/access-authn-authz/psp-to-pod-security-standards/>`__
-  `Enforce Pod Security Standards with Namespace Labels <https://kubernetes.io/docs/tasks/configure-pod-container/enforce-standards-namespace-labels/>`__
-  `Enforce Pod Security Standards by Configuring the Built-in Admission Controller <https://kubernetes.io/docs/tasks/configure-pod-container/enforce-standards-admission-controller/>`__
