:original_name: cce_bestpractice_0319.html

.. _cce_bestpractice_0319:

Configuration Suggestions on CCE Container Security
===================================================

Controlling the Pod Scheduling Scope
------------------------------------

The nodeSelector or nodeAffinity is used to limit the range of nodes to which applications can be scheduled, preventing the entire cluster from being threatened due to the exceptions of a single application.

To achieve strong isolation, like in logical multi-tenancy situations, it is important to have system add-ons run on separate nodes or node pools. This helps keep them separated from service pods and reduces the risk of privilege escalation within a cluster. To do this, you can set the node affinity policy to either **Node Affinity** or **Specified Node Pool Scheduling** on the add-on installation page.

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

Restricting the Access of Service Containers to the Management Plane
--------------------------------------------------------------------

To avoid unnecessary service interruption when restricting the service containers on a node from accessing the Kubernetes management plane, consider the following:

-  **Check whether any containers on the node require access to the cluster management plane.**

   Once you have restricted the service containers on the node from accessing the management plane, all containers on that node will be unable to access the kube-apiserver of the cluster. Before making the configuration, make sure that none of the containers on the node need to access the kube-apiserver of the cluster.

   Keep in mind that certain CCE add-ons, like CCE Advanced HPA, still require access to kube-apiserver. It is not recommended that you configure the access restriction on a node where such add-ons are running.

-  **Configure taints and affinity for the node.**

   If the service containers on the node do not need to access kube-apiserver, it is recommended that you configure labels and taints for the node. Additionally, configure `taints, tolerations <https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/>`__, and `node affinity <https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#node-affinity>`__ for the containers on the node. This will prevent other containers from being scheduled to that node, thus avoiding service exceptions.

To restrict the service containers on a node from accessing the management plane, take the following steps:

#. Obtain the container CIDR block and private API server address.

   On the **Clusters** page of the CCE console, click the name of the cluster to find the information on the details page.

#. Configure access rules.

   -  CCE cluster: Log in to each node in the cluster as user **root** and run the following command:

      -  VPC network

         .. code-block::

            iptables -I OUTPUT -s {container_cidr} -d {Private API server IP} -j REJECT

      -  Container tunnel network

         .. code-block::

            iptables -I FORWARD -s {container_cidr} -d {Private API server IP} -j REJECT

      *{container_cidr}* indicates the container CIDR of the cluster, for example, 10.0.0.0/16.

      To ensure configuration persistence, write the command to the **/etc/rc.local** script.

   -  CCE Turbo cluster: Add an outbound rule to the ENI security group of the cluster.

      a. Log in to the VPC console.
      b. In the navigation pane, choose **Access Control** > **Security Groups**.
      c. Locate the ENI security group corresponding to the cluster and name it in the format of *{Cluster name}*\ **-cce-eni-**\ *{Random ID}*. Click the security group name and configure rules.
      d. Click the **Outbound Rules** tab and click **Add Rule** to add an outbound rule for the security group.

         -  **Priority**: Set it to **1**.
         -  **Action**: Select **Deny**, indicating that the access to the destination address is denied.
         -  **Type**: Select **IPv4**.
         -  **Protocol & Port**: Enter **5443** based on the port in the intranet API server address.
         -  **Destination**: Select **IP address** and enter the IP address of the internal API server.

      e. Click **OK**.

#. Run the following command in the container to access kube-apiserver and check whether the request is intercepted:

   .. code-block::

      curl -k https://{Private API server IP}:5443

Properly Setting Volume Propagation
-----------------------------------

When mounting a host path, set the propagation mode to **None**. Exercise caution when using the **Bidirectional** mode.

The **mountPropagation** field in **Container.volumeMounts** controls the mount propagation feature of a volume. Value options are as follows:

-  **None**: the default value, equal to the **private** mount propagation option described in `Linux kernel documentation <https://www.kernel.org/doc/Documentation/filesystems/sharedsubtree.txt>`__. This volume mount will not receive any subsequent mounts that are mounted to this volume or any of its subdirectories by the host. In similar fashion, no mounts created by the container will be visible on the host.

-  **HostToContainer**: equal to the **rslave** mount propagation option described in `Linux kernel documentation <https://www.kernel.org/doc/Documentation/filesystems/sharedsubtree.txt>`__. This volume mount will receive all subsequent mounts that are mounted to this volume or any of its subdirectories. In other words, if the host mounts anything inside the volume mount, the container will see it mounted there. If a Bidirectional pod mounts anything to the same volume, this change is visible to all HostToContainer pods.

-  **Bidirectional**: equal to the **rshared** mount propagation option described in `Linux kernel documentation <https://www.kernel.org/doc/Documentation/filesystems/sharedsubtree.txt>`__. All volume mounts created by the container will be propagated back to the host and to all containers of all pods that use the same volume.

   Bidirectional mount propagation can be dangerous. It can damage the host operating system and therefore it is allowed only in privileged containers. An example is as follows:

   .. code-block::

      apiVersion: v1
      kind: Pod
      metadata:
          name: security-mount
          label:
            app: security-mount
      spec:
          containers:
          - name: security-mount
            image: ...
            volumeMounts:
            - name: mount-none
              mountPath: /opt
              mountPropagation: None
          volumes:
          - name: mount-none
            hostPath:
              path: /opt/

For details, see the `community documentation <https://kubernetes.io/docs/concepts/storage/volumes/>`__.
