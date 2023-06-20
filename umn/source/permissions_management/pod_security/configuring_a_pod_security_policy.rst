:original_name: cce_10_0275.html

.. _cce_10_0275:

Configuring a Pod Security Policy
=================================

A pod security policy (PSP) is a cluster-level resource that controls sensitive security aspects of the pod specification. The PodSecurityPolicy object in Kubernetes defines a group of conditions that a pod must comply with to be accepted by the system, as well as the default values of related fields.

By default, the PSP access control component is enabled for clusters of v1.17.17 and a global default PSP named **psp-global** is created. You can modify the default policy (but not delete it). You can also create a PSP and bind it to the RBAC configuration.

.. note::

   -  In addition to the global default PSP, the system configures independent PSPs for system components in namespace kube-system. Modifying the psp-global configuration does not affect pod creation in namespace kube-system.
   -  In Kubernetes 1.25, PSP has been removed and replaced by Pod Security Admission. For details, see :ref:`Configuring Pod Security Admission <cce_10_0466>`.

Modifying the Global Default PSP
--------------------------------

Before modifying the global default PSP, ensure that a CCE cluster has been created and connected by using kubectl.

#. Run the following command:

   **kubectl edit psp psp-global**

#. Modify the parameters as required. For details, see `PodSecurityPolicy <https://kubernetes.io/docs/tasks/administer-cluster/sysctl-cluster/>`__.

.. _cce_10_0275__section155111941177:

Example of Enabling Unsafe Sysctls in Pod Security Policy
---------------------------------------------------------

You can configure allowed-unsafe-sysctls for a node pool. For CCE **v1.17.17** and later versions, add configurations in **allowedUnsafeSysctls** of the pod security policy to make the configuration take effect. For details, see `PodSecurityPolicy <https://kubernetes.io/docs/tasks/administer-cluster/sysctl-cluster/>`__.

In addition to modifying the global pod security policy, you can add new pod security policies. For example, enable the **net.core.somaxconn** unsafe sysctls. The following is an example of adding a pod security policy:

.. code-block::

   apiVersion: policy/v1beta1
   kind: PodSecurityPolicy
   metadata:
     annotations:
       seccomp.security.alpha.kubernetes.io/allowedProfileNames: '*'
     name: sysctl-psp
   spec:
     allowedUnsafeSysctls:
     - net.core.somaxconn
     allowPrivilegeEscalation: true
     allowedCapabilities:
     - '*'
     fsGroup:
       rule: RunAsAny
     hostIPC: true
     hostNetwork: true
     hostPID: true
     hostPorts:
     - max: 65535
       min: 0
     privileged: true
     runAsGroup:
       rule: RunAsAny
     runAsUser:
       rule: RunAsAny
     seLinux:
       rule: RunAsAny
     supplementalGroups:
       rule: RunAsAny
     volumes:
     - '*'
   ---
   kind: ClusterRole
   apiVersion: rbac.authorization.k8s.io/v1
   metadata:
     name: sysctl-psp
   rules:
     - apiGroups:
         - "*"
       resources:
         - podsecuritypolicies
       resourceNames:
         - sysctl-psp
       verbs:
         - use

   ---
   apiVersion: rbac.authorization.k8s.io/v1
   kind: ClusterRoleBinding
   metadata:
     name: sysctl-psp
   roleRef:
     kind: ClusterRole
     name: sysctl-psp
     apiGroup: rbac.authorization.k8s.io
   subjects:
   - kind: Group
     name: system:authenticated
     apiGroup: rbac.authorization.k8s.io

Restoring the Original PSP
--------------------------

If you have modified the default pod security policy and want to restore the original pod security policy, perform the following operations.

#. Create a policy description file named **policy.yaml**. **policy.yaml** is an example file name. You can rename it as required.

   **vi policy.yaml**

   The content of the description file is as follows:

   .. code-block::

      apiVersion: policy/v1beta1
      kind: PodSecurityPolicy
      metadata:
        name: psp-global
        annotations:
          seccomp.security.alpha.kubernetes.io/allowedProfileNames: '*'
      spec:
        privileged: true
        allowPrivilegeEscalation: true
        allowedCapabilities:
          - '*'
        volumes:
          - '*'
        hostNetwork: true
        hostPorts:
          - min: 0
            max: 65535
        hostIPC: true
        hostPID: true
        runAsUser:
          rule: 'RunAsAny'
        seLinux:
          rule: 'RunAsAny'
        supplementalGroups:
          rule: 'RunAsAny'
        fsGroup:
          rule: 'RunAsAny'

      ---
      kind: ClusterRole
      apiVersion: rbac.authorization.k8s.io/v1
      metadata:
        name: psp-global
      rules:
        - apiGroups:
            - "*"
          resources:
            - podsecuritypolicies
          resourceNames:
            - psp-global
          verbs:
            - use

      ---
      apiVersion: rbac.authorization.k8s.io/v1
      kind: ClusterRoleBinding
      metadata:
        name: psp-global
      roleRef:
        kind: ClusterRole
        name: psp-global
        apiGroup: rbac.authorization.k8s.io
      subjects:
      - kind: Group
        name: system:authenticated
        apiGroup: rbac.authorization.k8s.io

#. Run the following command:

   **kubectl apply -f policy.yaml**
