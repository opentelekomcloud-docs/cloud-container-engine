:original_name: cce_10_0048.html

.. _cce_10_0048:

Creating a StatefulSet
======================

Scenario
--------

StatefulSets are a type of workloads whose data or status is stored while they are running. For example, MySQL is a StatefulSet because it needs to store new data.

A container can be migrated between different hosts, but data is not stored on the hosts. To store StatefulSet data persistently, attach HA storage volumes provided by CCE to the container.

Constraints
-----------

-  When you delete or scale a StatefulSet, the system does not delete the storage volumes associated with the StatefulSet to ensure data security.
-  When you delete a StatefulSet, reduce the number of replicas to **0** before deleting the StatefulSet so that pods in the StatefulSet can be stopped in order.
-  When you create a StatefulSet, a headless Service is required for pod access. For details, see :ref:`Headless Service <cce_10_0398>`.
-  When a node is unavailable, pods become **Unready**. In this case, manually delete the pods of the StatefulSet so that the pods can be migrated to a normal node.

Prerequisites
-------------

-  Before creating a workload, you must have an available cluster. For details on how to create a cluster, see :ref:`Creating a CCE Cluster <cce_10_0028>`.
-  To enable public access to a workload, ensure that an EIP or load balancer has been bound to at least one node in the cluster.

   .. note::

      If a pod has multiple containers, ensure that the ports used by the containers do not conflict with each other. Otherwise, creating the StatefulSet will fail.

Using the CCE Console
---------------------

#. Log in to the CCE console.

#. Click the cluster name to go to the cluster console, choose **Workloads** in the navigation pane, and click the **Create Workload** in the upper right corner.

#. Set basic information about the workload.

   **Basic Info**

   -  **Workload Type**: Select **StatefulSet**. For details about workload types, see :ref:`Overview <cce_10_0006>`.
   -  **Workload Name**: Enter the name of the workload. Enter 1 to 63 characters starting with a lowercase letter and ending with a lowercase letter or digit. Only lowercase letters, digits, and hyphens (-) are allowed.
   -  **Namespace**: Select the namespace of the workload. The default value is **default**. You can also click **Create Namespace** to create one. For details, see :ref:`Creating a Namespace <cce_10_0278>`.
   -  **Pods**: Enter the number of pods of the workload.
   -  **Container Runtime**: A CCE standard cluster uses runC by default, whereas a CCE Turbo cluster supports both runC and Kata. For details about the differences, see :ref:`Kata Runtime and Common Runtime <cce_10_0463>`.
   -  **Time Zone Synchronization**: Specify whether to enable time zone synchronization. After time zone synchronization is enabled, the container and node use the same time zone. The time zone synchronization function depends on the local disk mounted to the container. Do not modify or delete the time zone. For details, see :ref:`Configuring Time Zone Synchronization <cce_10_0354>`.

   **Container Settings**

   -  Container Information

      Multiple containers can be configured in a pod. You can click **Add Container** on the right to configure multiple containers for the pod.

      -  **Basic Info**: Configure basic information about the container.

         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                         |
         +===================================+=====================================================================================================================================================================================================================================================================================================================================================================================================================================+
         | Container Name                    | Name the container.                                                                                                                                                                                                                                                                                                                                                                                                                 |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Pull Policy                       | Image update or pull policy. If you select **Always**, the image is pulled from the image repository each time. If you do not select **Always**, the existing image of the node is preferentially used. If the image does not exist, the image is pulled from the image repository.                                                                                                                                                 |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Image Name                        | Click **Select Image** and select the image used by the container.                                                                                                                                                                                                                                                                                                                                                                  |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | To use a third-party image, see :ref:`Using Third-Party Images <cce_10_0009>`.                                                                                                                                                                                                                                                                                                                                                      |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Image Tag                         | Select the image tag to be deployed.                                                                                                                                                                                                                                                                                                                                                                                                |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | CPU Quota                         | -  **Request**: minimum number of CPU cores required by a container. The default value is 0.25 cores.                                                                                                                                                                                                                                                                                                                               |
         |                                   | -  **Limit**: maximum number of CPU cores available for a container. Do not leave **Limit** unspecified. Otherwise, intensive use of container resources will occur and your workload may exhibit unexpected behavior.                                                                                                                                                                                                              |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | If **Request** and **Limit** are not specified, the quota is not limited. For more information and suggestions about **Request** and **Limit**, see :ref:`Configuring Container Specifications <cce_10_0163>`.                                                                                                                                                                                                                      |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Memory Quota                      | -  **Request**: minimum amount of memory required by a container. The default value is 512 MiB.                                                                                                                                                                                                                                                                                                                                     |
         |                                   | -  **Limit**: maximum amount of memory available for a container. When memory usage exceeds the specified memory limit, the container will be terminated.                                                                                                                                                                                                                                                                           |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | If **Request** and **Limit** are not specified, the quota is not limited. For more information and suggestions about **Request** and **Limit**, see :ref:`Configuring Container Specifications <cce_10_0163>`.                                                                                                                                                                                                                      |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | (Optional) GPU Quota              | Configurable only when the cluster contains GPU nodes and the :ref:`CCE AI Suite (NVIDIA GPU) <cce_10_0141>` add-on is installed.                                                                                                                                                                                                                                                                                                   |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | -  **All**: No GPU will be used.                                                                                                                                                                                                                                                                                                                                                                                                    |
         |                                   | -  **Dedicated**: GPU resources are dedicated for the container.                                                                                                                                                                                                                                                                                                                                                                    |
         |                                   | -  **Shared**: percentage of GPU resources used by the container. For example, if this parameter is set to **10%**, the container uses 10% of GPU resources.                                                                                                                                                                                                                                                                        |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | For details about how to use GPUs in the cluster, see :ref:`Default GPU Scheduling in Kubernetes <cce_10_0345>`.                                                                                                                                                                                                                                                                                                                    |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | (Optional) Privileged Container   | Programs in a privileged container have certain privileges.                                                                                                                                                                                                                                                                                                                                                                         |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | If **Privileged Container** is enabled, the container is assigned privileges. For example, privileged containers can manipulate network devices on the host machine and modify kernel parameters.                                                                                                                                                                                                                                   |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | (Optional) Init Container         | Whether to use the container as an init container. An init container does not support health check.                                                                                                                                                                                                                                                                                                                                 |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   | An init container is a special container that runs before other app containers in a pod are started. Each pod can contain multiple containers. In addition, a pod can contain one or more init containers. Application containers in a pod are started and run only after the running of all init containers completes. For details, see `Init Containers <https://kubernetes.io/docs/concepts/workloads/pods/init-containers/>`__. |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

      -  (Optional) **Lifecycle**: Configure operations to be performed in a specific phase of the container lifecycle, such as Startup Command, Post-Start, and Pre-Stop. For details, see :ref:`Configuring Container Lifecycle Parameters <cce_10_0105>`.

      -  (Optional) **Health Check**: Set the liveness probe, ready probe, and startup probe as required. For details, see :ref:`Configuring Container Health Check <cce_10_0112>`.

      -  (Optional) **Environment Variables**: Configure variables for the container running environment using key-value pairs. These variables transfer external information to containers running in pods and can be flexibly modified after application deployment. For details, see :ref:`Configuring Environment Variables <cce_10_0113>`.

      -  (Optional) **Data Storage**: Mount local storage or cloud storage to the container. The application scenarios and mounting modes vary with the storage type. For details, see :ref:`Storage <cce_10_0374>`.

         .. note::

            -  StatefulSets support dynamic attachment of EVS disks. For details, see :ref:`Dynamically Mounting an EVS Disk to a StatefulSet <cce_10_0616>` and :ref:`Dynamically Mounting a Local PV to a StatefulSet <cce_10_0635>`.

               Dynamic mounting is achieved by using the `volumeClaimTemplates <https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/#volume-claim-templates>`__ field and depends on the dynamic creation capability of StorageClass. A StatefulSet associates each pod with a PVC using the **volumeClaimTemplates** field, and the PVC is bound to the corresponding PV. Therefore, after the pod is rescheduled, the original data can still be mounted based on the PVC name.

            -  After a workload is created, the storage that is dynamically mounted cannot be updated.

      -  (Optional) **Security Context**: Assign container permissions to protect the system and other containers from being affected. Enter the user ID to assign container permissions and prevent systems and other containers from being affected.

      -  (Optional) **Logging**: Report standard container output logs to AOM by default, without requiring manual settings. You can manually configure the log collection path. For details, see :ref:`Connecting CCE to AOM <cce_10_0018>`.

         To disable the standard output of the current workload, add the annotation **kubernetes.AOM.log.stdout: []** in :ref:`Labels and Annotations <cce_10_0047__li179714209414>`. For details about how to use this annotation, see :ref:`Table 1 <cce_10_0386__table194691458405>`.

   -  **Image Access Credential**: Select the credential used for accessing the image repository. The default value is **default-secret**. You can use default-secret to access images in SWR. For details about **default-secret**, see :ref:`default-secret <cce_10_0388__section11760122012591>`.

   -  (Optional) **GPU**: **All** is selected by default. The workload instance will be scheduled to the node of the specified GPU type.

   **Headless Service Parameters**

   A headless Service is used to solve the problem of mutual access between pods in a StatefulSet. The headless Service provides a fixed access domain name for each pod. For details, see :ref:`Headless Service <cce_10_0398>`.

   **(Optional) Service Settings**

   A Service provides external access for pods. With a static IP address, a Service forwards access traffic to pods and automatically balances load for these pods.

   You can also create a Service after creating a workload. For details about Services of different types, see :ref:`Overview <cce_10_0249>`.

   **(Optional) Advanced Settings**

   -  **Upgrade**: Specify the upgrade mode and parameters of the workload. **Rolling upgrade** and **Replace upgrade** are available. For details, see :ref:`Workload Upgrade Policies <cce_10_0397>`.

   -  **Pod Management Policies**

      For some distributed systems, the StatefulSet sequence is unnecessary and/or should not occur. These systems require only uniqueness and identifiers.

      -  **OrderedReady**: The StatefulSet will deploy, delete, or scale pods in order and one by one. (The StatefulSet continues only after the previous pod is ready or deleted.) This is the default policy.
      -  **Parallel**: The StatefulSet will create pods in parallel to match the desired scale without waiting, and will delete all pods at once.

   -  **Scheduling**: Configure affinity and anti-affinity policies for flexible workload scheduling. Load affinity and node affinity are provided.

      -  **Load Affinity**: Common load affinity policies are offered for quick load affinity deployment.

         -  **Multi-AZ deployment is preferred**: Workload pods are preferentially scheduled to nodes in different AZs through pod anti-affinity (**podAntiAffinity**). If all the nodes in the cluster are deployed in the same AZ, the pods will be scheduled to that AZ but onto different nodes for high availability. If there are fewer nodes than pods, the extra pods will fail to run.
         -  **Forcible multi-AZ deployment**: Workload pods are forcibly scheduled to nodes in different AZs through pod anti-affinity (**podAntiAffinity**). If there are fewer AZs than pods, the extra pods will fail to run.
         -  **Custom policies**: Affinity and anti-affinity policies can be customized as needed. For details, see :ref:`Scheduling Policies (Affinity/Anti-affinity) <cce_10_0232>`.

      -  **Node Affinity**: Common load affinity policies are offered for quick load affinity deployment.

         -  **Node Affinity**: Workload pods can be deployed on specified nodes through node affinity (**nodeAffinity**). If no node is specified, the pods will be randomly scheduled based on the default scheduling policy of the cluster.
         -  **Specified node pool scheduling**: Workload pods can be deployed in a specified node pool through node affinity (**nodeAffinity**). If no node pool is specified, the pods will be randomly scheduled based on the default scheduling policy of the cluster.
         -  **Custom policies**: Affinity and anti-affinity policies can be customized as needed. For details, see :ref:`Scheduling Policies (Affinity/Anti-affinity) <cce_10_0232>`.

   -  **Toleration**: Using both taints and tolerations allows (not forcibly) the pod to be scheduled to a node with the matching taints, and controls the pod eviction policies after the node where the pod is located is tainted. For details, see :ref:`Taints and Tolerations <cce_10_0728>`.

   -  **Labels and Annotations**: Add labels or annotations for pods using key-value pairs. After entering the key and value, click **Confirm**. For details about how to use and configure labels and annotations, see :ref:`Labels and Annotations <cce_10_0386>`.

   -  **DNS**: Configure a separate DNS policy for the workload. For details, see :ref:`DNS Configuration <cce_10_0365>`.

   -  **Network Configuration**

      -  Pod ingress/egress bandwidth limitation: You can set ingress/egress bandwidth limitation for pods. For details, see :ref:`Configuring QoS for a Pod <cce_10_0382>`.

#. Click **Create Workload** in the lower right corner.

Using kubectl
-------------

In this example, a Nginx workload is used and the EVS volume is dynamically mounted to it using the **volumeClaimTemplates** field.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create and edit the **nginx-statefulset.yaml** file.

   **nginx-statefulset.yaml** is an example file name, and you can change it as required.

   **vi nginx-statefulset.yaml**

   The following provides an example of the file contents. For more information on StatefulSet, see the `Kubernetes documentation <https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/>`__.

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
              storageClassName: csi-disk # Storage class name. The value is csi-disk for the EVS volume.
        updateStrategy:
          type: RollingUpdate

   **vi nginx-headless.yaml**

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

#. Create a workload and the corresponding headless service.

   **kubectl create -f nginx-statefulset.yaml**

   If the following information is displayed, the StatefulSet has been successfully created.

   .. code-block::

      statefulset.apps/nginx created

   **kubectl create -f nginx-headless.yaml**

   If the following information is displayed, the headless service has been successfully created.

   .. code-block::

      service/nginx-svc created

#. If the workload will be accessed through a ClusterIP or NodePort Service, configure the access mode. For details, see :ref:`Network <cce_10_0020>`.
