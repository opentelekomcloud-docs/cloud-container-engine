:original_name: cce_bestpractice_0319.html

.. _cce_bestpractice_0319:

Using Containers Securely in a CCE Cluster
==========================================

Controlling the Pod Scheduling Scope
------------------------------------

The nodeSelector or nodeAffinity is used to limit the range of nodes to which applications can be scheduled, preventing the entire cluster from being threatened due to the exceptions of a single application.

To achieve strong isolation, like in logical multi-tenancy situations, it is important to have system add-ons run on separate nodes or node pools. This helps keep them separated from service pods and reduces the risk of privilege escalation within a cluster. To do this, you can set the node affinity policy to either **Specify node** or **Specify node pool** on the add-on installation page.

Ensuring Container Security
---------------------------

-  Configure compute resource request and limit for a container. This prevents the container from occupying too many resources and affecting the stability of the node and other containers on the same node.
-  Unless necessary, do not mount sensitive node directories, such as **/**, **/boot**, **/dev**, **/etc**, **/lib**, **/proc**, **/sys**, and **/usr**, to a container.
-  Unless necessary, do not run the sshd process in a container.
-  Unless necessary, do not share the network namespace between containers and nodes.
-  Unless necessary, do not share the process namespace between containers and nodes.
-  Unless necessary, do not share the IPC namespace between containers and nodes.
-  Unless necessary, do not share the UTS namespace between containers and nodes.
-  Unless necessary, do not mount the sock file of Docker to any container.

Controlling Access Permissions
------------------------------

When using a containerized application, comply with the principle of least privilege (PoLP) and properly configure **securityContext** of Deployments or StatefulSets.

-  Configure runAsUser to specify a non-root user to run a container.

-  Configure privileged to prevent containers from being used in scenarios where privilege is not required.

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

Restricting Service Container Access to the Management Plane
------------------------------------------------------------

To avoid unnecessary service interruption when restricting the service containers on a node from accessing the Kubernetes management plane, consider the following:

-  **Check whether any containers on the node require access to the cluster management plane.**

   Once you have restricted the service containers on the node from accessing the management plane, all containers on that node will be unable to access the kube-apiserver of the cluster. Before making the configuration, make sure that none of the containers on the node need to access the kube-apiserver of the cluster.

   Keep in mind that certain CCE add-ons, like CCE Advanced HPA, still require access to kube-apiserver. It is not recommended that you configure the access restriction on a node where such add-on pods are running.

-  **Configure taints and affinity for the node.**

   If the service containers on the node do not need to access kube-apiserver, it is recommended that you configure labels and taints for the node. Additionally, configure `taints, tolerations <https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/>`__, and `node affinity <https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#node-affinity>`__ for the containers on the node. This will prevent other containers from being scheduled to that node, thus avoiding service exceptions.

To restrict the service containers on a node from accessing the management plane, take the following steps:

#. Obtain the container CIDR block and private API server address.

   On the **Clusters** page of the CCE console, click the name of the cluster to find the information on the details page.

#. Configure access rules.

   -  CCE standard cluster: Log in to each node in the cluster as user **root** and run the following command:

      -  VPC network

         .. code-block::

            iptables -I OUTPUT -s {container_cidr} -d {Private API server IP} -j REJECT

      -  Container tunnel network

         .. code-block::

            iptables -I FORWARD -s {container_cidr} -d {Private API server IP} -j REJECT

      *{container_cidr}* indicates the container CIDR block of the cluster, for example, **10.0.0.0/16**.

      To ensure configuration persistence, write the command to the **/etc/rc.local** script.

   -  CCE Turbo cluster: Add an outbound rule to the network interface security group of the cluster.

      a. Log in to the VPC console.
      b. In the navigation pane, choose **Access Control** > **Security Groups**.
      c. Locate the network interface security group of the cluster and name it in the format of *{Cluster name}*\ **-cce-eni-**\ *{Random ID}*. Click the security group name and configure rules.
      d. Click the **Outbound Rules** tab and click **Add Rule** to add an outbound rule to the security group.

         -  **Priority**: Set it to **1**.
         -  **Action**: Select **Deny**, indicating that the access to the destination address is denied.
         -  **Type**: Select **IPv4**.
         -  **Protocol & Port**: Enter **5443** based on the port in the intranet API server address.
         -  **Destination**: Select **IP address** and enter the IP address of the internal API server.

      e. Click **OK**.

#. Run the following command in the container to access kube-apiserver and check whether the request is intercepted:

   .. code-block::

      curl -k https://{Private API server IP}:5443

Properly Configuring Volume Propagation
---------------------------------------

When mounting a host path, set the propagation mode to **None**. **Bidirectional** should be used with caution.

The **mountPropagation** field in **Container.volumeMounts** controls the mount propagation behavior of a volume. Value options are:

-  **None**: the default value and corresponds to the **private** mount propagation option in the `Linux kernel documentation <https://www.kernel.org/doc/Documentation/filesystems/sharedsubtree.txt>`__. After the volume is mounted, the pod will not see any subsequent mount changes made on the node for this volume or any of its subdirectories. Likewise, any mounts created by the pod will not be visible on the node.

-  **HostToContainer**: corresponds to the **rslave** mount propagation option in the `Linux kernel documentation <https://www.kernel.org/doc/Documentation/filesystems/sharedsubtree.txt>`__. After the volume is mounted, the pod will see any subsequent mount operations performed on the node for this volume or its subdirectories. If the node mounts anything inside this volume, the pod will be able to see it. If a pod is configured with **Bidirectional** and mounts something on the same volume, pods using **HostToContainer** will also see those changes.

-  **Bidirectional**: corresponds to the **rshared** mount propagation option in the `Linux kernel documentation <https://www.kernel.org/doc/Documentation/filesystems/sharedsubtree.txt>`__. Mounts created inside the pod are propagated back to the node and to all containers in all pods that use the same volume.

   Using **Bidirectional** can damage the node OS. So it is only allowed in privileged containers. An example is as follows:

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
