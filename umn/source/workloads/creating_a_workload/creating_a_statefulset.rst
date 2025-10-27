:original_name: cce_10_0048.html

.. _cce_10_0048:

Creating a StatefulSet
======================

A `StatefulSet <https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/>`__ is an application that needs to retain data or state while running. StatefulSets are ideal for stateful applications, such as databases, cache services, and message queues. Unlike Deployments, StatefulSets have the following features:

-  **Fixed identifier**: Each pod in a StatefulSet has a fixed identifier, which is associated with the pod name. The identifier is typically in the format of **<StatefulSet name>-<Ordinal>**. For example, in a StatefulSet named **web**, the pods would have the names like **web-0** and **web-1**. This naming rule enables each pod to maintain its identity and persistent data even after restart or migration.
-  **Ordered deployment and scaling**: Pods in a StatefulSet are created, scaled, or deleted sequentially. For example, pods with higher ordinals are prioritized for scale-out and removed first during scale-in, which is critical to stateful applications that need to be started or stopped in sequence and ideal for scenarios like primary-secondary replication of databases.
-  **Persistent storage**: To ensure data persistence, you can assign stable persistent storage volumes to each StatefulSet pod by using **VolumeClaimTemplate**. If a pod is scheduled to other nodes, its original data volume remains intact via the PVC, preventing data loss.

Notes and Constraints
---------------------

-  When you delete or scale a StatefulSet, the system does not delete the storage volumes associated with the StatefulSet to ensure data security.
-  When you delete a StatefulSet, reduce the number of replicas to **0** before deleting the StatefulSet so that pods in the StatefulSet can be stopped in order.
-  When you create a StatefulSet, a headless Service is required for pod access. For details, see :ref:`Headless Service <cce_10_0398>`.
-  When a node is unavailable, pods become **Unready**. In this case, manually delete the pods of the StatefulSet so that the pods can be migrated to a normal node.

Prerequisites
-------------

-  A cluster is available. For details about how to create a cluster, see :ref:`Creating a CCE Standard/Turbo Cluster <cce_10_0028>`.
-  There are some available nodes in the cluster. If no node is available, create one by referring to :ref:`Creating a Node <cce_10_0363>`.

Using the CCE Console
---------------------

#. Log in to the CCE console.

#. Click the cluster name to go to the cluster console, choose **Workloads** in the navigation pane, and click the **Create Workload** in the upper right corner.

#. Configure **basic information** about the workload.

   +---------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                 | Description                                                                                                                                                                                                                                                                                                                                    |
   +===========================+================================================================================================================================================================================================================================================================================================================================================+
   | Workload Type             | Select **StatefulSet**. For details about different workload types, see :ref:`Workload Overview <cce_10_0006>`.                                                                                                                                                                                                                                |
   +---------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Workload Name             | Enter a name for the workload. Enter 1 to 63 characters starting with a lowercase letter and ending with a lowercase letter or digit. Only lowercase letters, digits, and hyphens (-) are allowed.                                                                                                                                             |
   +---------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Namespace                 | Select a namespace for the workload. The default value is **default**. You can also click **Create Namespace** to create one. For details, see :ref:`Creating a Namespace <cce_10_0278>`.                                                                                                                                                      |
   +---------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Pods                      | Enter the number of workload pods.                                                                                                                                                                                                                                                                                                             |
   +---------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Container Runtime         | A CCE standard cluster uses a common runtime by default, whereas a CCE Turbo cluster supports both common and secure runtimes. For details about their differences, see :ref:`Secure Runtime and Common Runtime <cce_10_0463>`.                                                                                                                |
   +---------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Time Zone Synchronization | Configure whether to enable time zone synchronization. After this function is enabled, the container and node will share the same time zone. Time zone synchronization relies on the local disk mounted to the container. Do not modify or delete the local disk. For details, see :ref:`Configuring Time Zone Synchronization <cce_10_0354>`. |
   +---------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Configure **container settings** for the workload.

   -  **Container Information**: Click **Add Container** on the right to configure multiple containers for the pod.

      .. caution::

         If you configured multiple containers for a pod, ensure that the ports used by each container do not conflict with each other, or the workload cannot be deployed.

      -  **Basic Info**: Configure basic information about the container.

         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                         |
         +===================================+=====================================================================================================================================================================================================================================================================================================================================================================================================================================+
         | Container Name                    | Enter a name for the container.                                                                                                                                                                                                                                                                                                                                                                                                     |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Pull Policy                       | Image update or pull policy. If you select **Always**, the image is pulled from the image repository each time. If you do not select **Always**, the existing image of the node is preferentially used. If the image does not exist, the image is pulled from the image repository.                                                                                                                                                 |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Image Name                        | Click **Select Image** and select the image used by the container.                                                                                                                                                                                                                                                                                                                                                                  |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | To use a third-party image, directly enter image path. Ensure that the :ref:`image access credential <cce_10_0047__li1487514116369>` can be used to access the image repository. For details, see :ref:`Using Third-Party Images <cce_10_0009>`.                                                                                                                                                                                    |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Image Tag                         | Select the image tag to be deployed.                                                                                                                                                                                                                                                                                                                                                                                                |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | CPU Quota                         | -  **Request**: minimum number of CPU cores required by a container. The default value is 0.25 cores.                                                                                                                                                                                                                                                                                                                               |
         |                                   | -  **Limit**: maximum number of CPU cores that can be used by a container. This prevents containers from using excessive resources.                                                                                                                                                                                                                                                                                                 |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | If **Request** and **Limit** are not specified, the quota is not limited. For more information and suggestions about **Request** and **Limit**, see :ref:`Configuring Container Specifications <cce_10_0163>`.                                                                                                                                                                                                                      |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Memory Quota                      | -  **Request**: minimum amount of memory required by a container. The default value is 512 MiB.                                                                                                                                                                                                                                                                                                                                     |
         |                                   | -  **Limit**: maximum amount of memory available for a container. When memory usage exceeds the specified memory limit, the container will be terminated.                                                                                                                                                                                                                                                                           |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | If **Request** and **Limit** are not specified, the quota is not limited. For more information and suggestions about **Request** and **Limit**, see :ref:`Configuring Container Specifications <cce_10_0163>`.                                                                                                                                                                                                                      |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | (Optional) GPU Quota              | Configurable only when the cluster contains GPU nodes and the :ref:`CCE AI Suite (NVIDIA GPU) <cce_10_0141>` add-on has been installed.                                                                                                                                                                                                                                                                                             |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | -  **Do not use**: No GPU will be used.                                                                                                                                                                                                                                                                                                                                                                                             |
         |                                   | -  **GPU card**: The GPU is dedicated for the container.                                                                                                                                                                                                                                                                                                                                                                            |
         |                                   | -  **GPU Virtualization**: percentage of GPU resources used by the container. For example, if this parameter is set to **10%**, the container will use 10% of GPU resources.                                                                                                                                                                                                                                                        |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | For details about how to use GPUs in a cluster, see :ref:`Default GPU Scheduling in Kubernetes <cce_10_0345>`.                                                                                                                                                                                                                                                                                                                      |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | (Optional) Privileged Container   | Programs in a privileged container have certain privileges. If this option is enabled, the container will be assigned privileges. For example, privileged containers can manipulate network devices on the host machine, modify kernel parameters, access all devices on the node.                                                                                                                                                  |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | For more information, see `Pod Security Standards <https://kubernetes.io/docs/concepts/security/pod-security-standards/>`__.                                                                                                                                                                                                                                                                                                        |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | (Optional) Init Container         | Whether to use the container as an init container. An init container does not support health check.                                                                                                                                                                                                                                                                                                                                 |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | An init container is a special container that runs before other app containers in a pod are started. Each pod can contain multiple containers. In addition, a pod can contain one or more init containers. Application containers in a pod are started and run only after the running of all init containers completes. For details, see `Init Containers <https://kubernetes.io/docs/concepts/workloads/pods/init-containers/>`__. |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | (Optional) Run Option             | Add run options for the container. For details, see `Pod <https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/pod-v1/>`__. CCE supports the following run options:                                                                                                                                                                                                                                               |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | -  **stdin**: allows containers to receive input from external sources, such as terminals or other input streams.                                                                                                                                                                                                                                                                                                                   |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | -  **tty**: allocates a pseudo terminal to containers, allowing you to send commands to them as if you were using a local terminal.                                                                                                                                                                                                                                                                                                 |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   |    In most cases, tty is enabled along with stdin, indicating that the terminal (tty) is associated with the standard input (stdin) of the container. This allows for interactive operations, similar to the **kubectl exec -i -t** command. The difference is that this parameter has been configured when the pod is launched.                                                                                                    |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

      -  (Optional) **Lifecycle**: Configure operations to be performed in a specific phase of the container lifecycle, such as Startup Command, Post-Start, and Pre-Stop. For details, see :ref:`Configuring the Container Lifecycle <cce_10_0105>`.

      -  (Optional) **Health Check**: Set the liveness probe, ready probe, and startup probe as required. For details, see :ref:`Configuring Container Health Check <cce_10_0112>`.

      -  (Optional) **Environment Variables**: Configure variables for the container running environment using key-value pairs. These variables transfer external information to containers running in pods and can be flexibly modified after application deployment. For details, see :ref:`Configuring Environment Variables <cce_10_0113>`.

      -  (Optional) **Data Storage**: Mount local storage or cloud storage to the container. The application scenarios and mounting modes vary with the StorageClass. For details, see :ref:`Storage <cce_10_0374>`.

         .. note::

            -  StatefulSets support dynamic attachment of EVS disks. For details, see :ref:`Dynamically Mounting an EVS Disk to a StatefulSet <cce_10_0616>` or :ref:`Dynamically Mounting a Local PV to a StatefulSet <cce_10_0635>`.

               Dynamic mounting is achieved by using the `volumeClaimTemplates <https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/#volume-claim-templates>`__ field and depends on the dynamic creation capability of StorageClass. A StatefulSet associates each pod with a PVC using the **volumeClaimTemplates** field, and the PVC is bound to the corresponding PV. Therefore, after the pod is rescheduled, the original data can still be mounted based on the PVC name.

            -  After a workload is created, the dynamically mounted storage cannot be updated.

      -  (Optional) **Security Context**: Assign container permissions to protect the system and other containers from being affected. Enter the user ID to assign container permissions and prevent systems and other containers from being affected.

      -  (Optional) **Logging**: Report standard container output logs to AOM by default, without requiring manual settings. You can manually configure the log collection path. For details, see :ref:`Collecting Container Logs Using ICAgent <cce_10_0018>`.

         To disable the collection of the standard output logs of the current workload, add the annotation **kubernetes.AOM.log.stdout: []** in :ref:`Labels and Annotations <cce_10_0048__li9923141863518>` in the **Advanced Settings** area. For details about how to use this annotation, see :ref:`Table 1 <cce_10_0386__table19757101213714>`.

   -  **Image Access Credential**: Select the credential used for accessing the image repository. The default value is **default-secret**. You can use default-secret to access images in SWR Shared Edition. For details about **default-secret**, see :ref:`default-secret <cce_10_0388__section11760122012591>`.
   -  (Optional) **GPU**: **All** is selected by default. The workload instance will be scheduled to the node of the specified GPU type.

#. Configure the **settings of the headless Service** associated with the workload.

   A headless Service provides a fixed access domain name for every pod within a StatefulSet for mutual pod access. For details, see :ref:`Headless Service <cce_10_0398>`.

#. (Optional) Configure **the settings of the Service** associated with the workload.

   A Service provides external access for pods. With a static IP address, a Service forwards access traffic to pods and automatically balances load for these pods.

   You can also create a Service after creating a workload. For details about Services of different types, see :ref:`Service Overview <cce_10_0249>`.

#. .. _cce_10_0048__li9923141863518:

   (Optional) Configure **advanced settings** for the workload.

   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                            |
   +===================================+========================================================================================================================================================================================================================================================================================================================================================================================================================================+
   | Upgrade                           | Specify the upgrade mode and parameters of the workload. **Rolling upgrade** and **Replace upgrade** are available. For details, see :ref:`Upgrading and Rolling Back a Workload <cce_10_0397>`.                                                                                                                                                                                                                                       |
   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Pod Management Policies           | For some distributed systems, the StatefulSet ordering guarantees are unnecessary and/or undesirable. These systems require only uniqueness and identifiers.                                                                                                                                                                                                                                                                           |
   |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                        |
   |                                   | -  **OrderedReady**: This is the default policy. The StatefulSet will deploy, delete, or scale pods in order and one by one. It continues only after the previous pod is ready or deleted.                                                                                                                                                                                                                                             |
   |                                   | -  **Parallel**: The StatefulSet will create pods in parallel or delete all pods at once. Changes to the StatefulSets apply immediately on the pods.                                                                                                                                                                                                                                                                                   |
   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Scheduling                        | Configure affinity and anti-affinity policies for flexible workload scheduling. Load affinity and node affinity are provided.                                                                                                                                                                                                                                                                                                          |
   |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                        |
   |                                   | -  **Load Affinity**: Common load affinity policies are offered for quick load affinity deployment.                                                                                                                                                                                                                                                                                                                                    |
   |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                        |
   |                                   |    -  **Not configured**: No load affinity policy is configured.                                                                                                                                                                                                                                                                                                                                                                       |
   |                                   |    -  **Multi-AZ deployment preferred**: Workload pods are **preferentially** scheduled to nodes in different AZs through pod anti-affinity.                                                                                                                                                                                                                                                                                           |
   |                                   |    -  **Forcible multi-AZ deployment**: Workload pods are forcibly scheduled to nodes in different AZs through pod anti-affinity (**podAntiAffinity**). If there are fewer AZs than pods, the extra pods will fail to run.                                                                                                                                                                                                             |
   |                                   |    -  **Customize affinity**: Affinity and anti-affinity policies can be customized. For details, see :ref:`Configuring Workload Affinity or Anti-affinity Scheduling (podAffinity or podAntiAffinity) <cce_10_0893>`.                                                                                                                                                                                                                 |
   |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                        |
   |                                   | -  **Node Affinity**: Common node affinity policies are offered for quick load affinity deployment.                                                                                                                                                                                                                                                                                                                                    |
   |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                        |
   |                                   |    -  **Not configured**: No node affinity policy is configured.                                                                                                                                                                                                                                                                                                                                                                       |
   |                                   |    -  **Specify node**: Workload pods can be deployed on specified nodes through node affinity (**nodeAffinity**). If no node is specified, the pods will be randomly scheduled based on the default scheduling policy of the cluster.                                                                                                                                                                                                 |
   |                                   |    -  **Specify node pool**: Workload pods can be deployed in a specified node pool through node affinity (**nodeAffinity**). If no node pool is specified, the pods will be randomly scheduled based on the default scheduling policy of the cluster.                                                                                                                                                                                 |
   |                                   |    -  **Customize affinity**: Affinity and anti-affinity policies can be customized. For details, see :ref:`Configuring Node Affinity Scheduling (nodeAffinity) <cce_10_0892>`.                                                                                                                                                                                                                                                        |
   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Toleration                        | Using both taints and tolerations allows (not forcibly) the pod to be scheduled to a node with the matching taints, and controls the pod eviction policies after the node where the pod is located is tainted. For details, see :ref:`Configuring Tolerance Policies <cce_10_0728>`.                                                                                                                                                   |
   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Labels and Annotations            | Add labels or annotations for pods using key-value pairs. After the setting, click **Confirm**. For details about labels and annotations, see :ref:`Configuring Labels and Annotations <cce_10_0386>`.                                                                                                                                                                                                                                 |
   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | DNS                               | Configure a separate DNS policy for the workload. For details, see :ref:`DNS Configuration <cce_10_0365>`.                                                                                                                                                                                                                                                                                                                             |
   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Network Configuration             | -  Pod ingress/egress bandwidth limit: You can set ingress and egress bandwidth limits for pods. For details, see :ref:`Configuring QoS for a Pod <cce_10_0382>`.                                                                                                                                                                                                                                                                      |
   |                                   | -  Whether to enable the static IP address: available only for clusters that support this function. After this function is enabled, you can set the interval for reclaiming expired pod IP addresses. For details, see :ref:`Configuring a Static IP Address for a Pod <cce_10_0603>`.                                                                                                                                                 |
   |                                   | -  Whether to enable a specified container network configuration: available only for clusters that support this function. After you enable a specified container network configuration, the workload will be created using the container subnet and security group in the configuration. For details, see :ref:`Binding a Subnet and Security Group to a Namespace or Workload Using a Container Network Configuration <cce_10_0196>`. |
   |                                   | -  Specify the container network configuration name: Only the custom container network configuration whose associated resource type is workload can be selected.                                                                                                                                                                                                                                                                       |
   |                                   | -  IPv6 shared bandwidth: available only for clusters that support this function. After this function is enabled, you can configure a shared bandwidth for a pod with IPv6 dual-stack network interfaces. For details, see :ref:`Configuring Shared Bandwidth for a Pod with IPv6 Dual-Stack Network Interfaces <cce_10_0604>`.                                                                                                        |
   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Create Workload** in the lower right corner. After a period of time, the workload enters the **Running** state.

Using kubectl
-------------

In this example, a Nginx workload is used and the EVS volume is dynamically mounted to it using the **volumeClaimTemplates** field.

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Create and edit the **nginx-statefulset.yaml** file.

   **nginx-statefulset.yaml** is an example file name, and you can change it as required.

   .. code-block::

      vi nginx-statefulset.yaml

   The below content shows only an example. For details about StatefulSets, see the `Kubernetes official documentation <https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/>`__.

   .. code-block::

      apiVersion: apps/v1
      kind: StatefulSet
      metadata:
        name: nginx
      spec:
        selector:
          matchLabels:
            app: nginx
        template:
          metadata:
            labels:
              app: nginx
          spec:
            containers:
              - name: container-1
                image: nginx:latest
                imagePullPolicy: IfNotPresent
                resources:
                  requests:
                    cpu: 250m
                    memory: 512Mi
                  limits:
                    cpu: 250m
                    memory: 512Mi
                volumeMounts:
                  - name: test
                    readOnly: false
                    mountPath: /usr/share/nginx/html
                    subPath: ''
            imagePullSecrets:
              - name: default-secret
            dnsPolicy: ClusterFirst
            volumes: []
        serviceName: nginx-svc
        replicas: 2
        volumeClaimTemplates:  # Dynamically mounts the EVS volume to the workload.
          - apiVersion: v1
            kind: PersistentVolumeClaim
            metadata:
              name: test
              namespace: default
              annotations:
                everest.io/disk-volume-type: SAS  # SAS EVS volume type.
              labels:
                failure-domain.beta.kubernetes.io/region: eu-de  # region where the EVS volume is created.
                failure-domain.beta.kubernetes.io/zone:    # AZ where the EVS volume is created. It must be the same as the AZ of the node.
            spec:
              accessModes:
                - ReadWriteOnce  # The value must be ReadWriteOnce for the EVS volume.
              resources:
                requests:
                  storage: 10Gi
              storageClassName: csi-disk # StorageClass name. The value is csi-disk for the EVS volume.
        updateStrategy:
          type: RollingUpdate

   Create the **nginx-headless.yaml** file.

   .. code-block::

      vi nginx-headless.yaml

   File content:

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        name: nginx-svc
        namespace: default
        labels:
          app: nginx
      spec:
        selector:
          app: nginx
          version: v1
        clusterIP: None
        ports:
          - name: nginx
            targetPort: 80
            nodePort: 0
            port: 80
            protocol: TCP
        type: ClusterIP

#. Create a workload.

   .. code-block::

      kubectl create -f nginx-statefulset.yaml

   If information similar to the following is displayed, the StatefulSet has been created:

   .. code-block::

      statefulset.apps/nginx created

   Create a headless Service.

   .. code-block::

      kubectl create -f nginx-headless.yaml

   If the following information is displayed, the headless service has been successfully created.

   .. code-block::

      service/nginx-svc created

#. If the workload will be accessed through a ClusterIP or NodePort Service, configure the access mode. For details, see :ref:`Networking <cce_10_0020>`.
