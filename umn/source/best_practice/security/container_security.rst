:original_name: cce_bestpractice_0319.html

.. _cce_bestpractice_0319:

Container Security
==================

Controlling the Pod Scheduling Scope
------------------------------------

The nodeSelector or nodeAffinity is used to limit the range of nodes to which applications can be scheduled, preventing the entire cluster from being threatened due to the exceptions of a single application.

Suggestions on Container Security Configuration
-----------------------------------------------

-  Set the computing resource limits (**request** and **limit**) of a container. This prevents the container from occupying too many resources and affecting the stability of the host and other containers on the same node.
-  Unless necessary, do not mount sensitive host directories to containers, such as **/**, **/boot**, **/dev**, **/etc**, **/lib**, **/proc**, **/sys**, and **/usr**.
-  Do not run the sshd process in containers unless necessary.
-  Unless necessary, it is not recommended that containers and hosts share the network namespace.
-  Unless necessary, it is not recommended that containers and hosts share the process namespace.
-  Unless necessary, it is not recommended that containers and hosts share the IPC namespace.
-  Unless necessary, it is not recommended that containers and hosts share the UTS namespace.
-  Unless necessary, do not mount the sock file of Docker to any container.

Container Permission Access Control
-----------------------------------

When using a containerized application, comply with the minimum privilege principle and properly set securityContext of Deployments or StatefulSets.

-  Configure runAsUser to specify a non-root user to run a container.

-  Configure privileged to prevent containers being used in scenarios where privilege is not required.

-  Configure capabilities to accurately control the privileged access permission of containers.

-  Configure allowPrivilegeEscalation to disable privilege escape in scenarios where privilege escalation is not required for container processes.

-  Configure seccomp to restrict the container syscalls. For details, see `Restrict a Container's Syscalls with seccomp <https://kubernetes.io/docs/tutorials/security/seccomp/>`__ in the official Kubernetes documentation.

-  Configure ReadOnlyRootFilesystem to protect the root file system of a container.

   Example YAML for a Deployment:

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: security-context-example
        namespace: security-example
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: security-context-example
            label: security-context-example
        strategy:
          rollingUpdate:
            maxSurge: 25%
            maxUnavailable: 25%
          type: RollingUpdate
        template:
          metadata:
            annotations:
              seccomp.security.alpha.kubernetes.io/pod: runtime/default
            labels:
              app: security-context-example
              label: security-context-example
          spec:
            containers:
              - image: ...
                imagePullPolicy: Always
                name: security-context-example
                securityContext:
                  allowPrivilegeEscalation: false
                  readOnlyRootFilesystem: true
                  runAsUser: 1000
                  capabilities:
                    add:
                    - NET_BIND_SERVICE
                    drop:
                    - all
                volumeMounts:
                  - mountPath: /etc/localtime
                    name: localtime
                    readOnly: true
                  - mountPath: /opt/write-file-dir
                    name: tmpfs-example-001
            securityContext:
              seccompProfile:
                type: RuntimeDefault
            volumes:
              - hostPath:
                  path: /etc/localtime
                  type: ""
                name: localtime
              - emptyDir: {}
                name: tmpfs-example-001

Restricting the Access of Containers to the Management Plane
------------------------------------------------------------

If application containers on a node do not need to access Kubernetes, you can perform the following operations to disable containers from accessing kube-apiserver:

#. Query the container CIDR block and private API server address.

   On the **Clusters** page of the CCE console, click the name of the cluster to find the information on the details page.

#. Log in to each node in the CCE cluster as user **root** and run the following command:

   -  VPC network:

      .. code-block::

         iptables -I OUTPUT -s {container_cidr} -d {Private API server IP} -j REJECT

   -  Container tunnel network:

      .. code-block::

         iptables -I FORWARD -s {container_cidr} -d {Private API server IP} -j REJECT

   *{container_cidr}* indicates the container CIDR of the cluster, for example, 10.0.0.0/16.

   To ensure configuration persistence, you are advised to write the command to the **/etc/rc.local** script.

#. Run the following command in the container to access kube-apiserver and check whether the request is intercepted:

   .. code-block::

      curl -k https://{Private API server IP}:5443
