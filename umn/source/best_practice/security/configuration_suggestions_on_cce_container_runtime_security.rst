:original_name: cce_bestpractice_10046.html

.. _cce_bestpractice_10046:

Configuration Suggestions on CCE Container Runtime Security
===========================================================

Container technology uses Linux namespaces and cgroups to isolate and control resources between containers and nodes. Namespaces provide kernel-level isolation, allowing processes to be restricted from accessing specific sets of resources, such as file systems, networks, processes, and users. Cgroups are a Linux kernel feature that manages and limits the usage of resources, such as CPU, memory, disk, and network, to prevent a single process from consuming too many resources and negatively impacting the overall system performance.

While namespaces and cgroups isolate resources between containers and nodes in an environment, node resources are not visible to containers. However, this isolation does not provide true security isolation because containers share the kernels of their nodes. If a container exhibits malicious behavior or a kernel vulnerability is exploited by attackers, the container may breach resource isolation. This can result in the container escaping and potentially compromising the node and other containers on the node.

To enhance runtime security, there are various mechanisms that can be used to detect and prevent malicious activities in containers. These mechanisms, such as capabilities, seccomp, AppArmor, and SELinux, can be integrated into Kubernetes. By using these mechanisms, container security can be improved and potential threats can be minimized.

Capabilities
------------

Capabilities are a permission mechanism that enables a process to perform certain system operations without requiring full root permissions. This mechanism divides root permissions into smaller, independent permissions known as capabilities. By doing so, the process only obtains the minimum permission set necessary to complete its tasks. This approach enhances system security and helps mitigate potential security risks.

In a containerized environment, you can manage a container's capabilities by configuring its **securityContext**. The following is a configuration example:

.. code-block::

   ...
   securityContext:
     capabilities:
       add:
       - NET_BIND_SERVICE
       drop:
       - all

In this way, you can ensure that the container only has the necessary permissions to complete its tasks. This approach eliminates the risk of security breaches caused by excessive permissions. For more information about how to configure capabilities for a container, see `Set capabilities for a Container <https://kubernetes.io/docs/tasks/configure-pod-container/security-context/#set-capabilities-for-a-container>`__.

Seccomp
-------

Seccomp is a mechanism that filters system calls, limiting the ones that processes can use to decrease the potential attack surface. Linux has many system calls, but not all are needed for containerized applications. By restricting the system calls that containers can execute, you can greatly reduce the risk of attacks on your applications.

Seccomp's main principle is to intercept all system calls and only allow trusted ones to pass. Container runtimes, such as Docker and containerd, come with default seccomp configurations that work for most common workloads.

In Kubernetes clusters, you can configure seccomp policies for containers to use the default security configuration. The following shows how to configure seccomp in different versions of Kubernetes clusters:

-  For clusters of versions earlier than Kubernetes 1.19, you can use the following annotations to specify the seccomp configuration:

   .. code-block::

      annotations:
        seccomp.security.alpha.kubernetes.io/pod: "runtime/default"

-  For clusters of Kubernetes 1.19 and later versions, you can use **securityContext** to configure seccomp policies.

   .. code-block::

      securityContext:
        seccompProfile:
          type: RuntimeDefault

These configurations use the default seccomp policy, which permits containers to make a limited number of secure system calls. For more configuration options and advanced settings of seccomp, see `Restrict a Container's Syscalls with seccomp <https://kubernetes.io/docs/tutorials/security/seccomp/>`__.

AppArmor and SELinux
--------------------

AppArmor and SELinux are both Mandatory Access Control (MAC) systems that offer a more stringent approach than traditional Discretionary Access Control (DAC) to manage and restrict process permissions. While similar to seccomp in concept, these systems provide more precise access control, including access to file system paths, network ports, and other resources.

AppArmor and SELinux enable administrators to create policies that precisely manage the resources that applications can access. They can limit read and write permissions on specific files or directories, or regulate access to network ports.

Both systems are integrated into Kubernetes, allowing security policies to be applied at the container level.

-  For details about how to use AppArmor, see `Restrict a Container's Access to Resources with AppArmor <https://kubernetes.io/docs/tutorials/security/apparmor/>`__.

-  For SELinux, you can configure **seLinuxOptions** in **securityContext**.

   .. code-block::

      ...
      securityContext:
        seLinuxOptions:
          level: "s0:c123,c456"

   For details, see `Assign SELinux labels to a Container <https://kubernetes.io/docs/tasks/configure-pod-container/security-context/#assign-selinux-labels-to-a-container>`__.
