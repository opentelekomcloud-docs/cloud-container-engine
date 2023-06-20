:original_name: cce_10_0048.html

.. _cce_10_0048:

Creating a StatefulSet
======================

Scenario
--------

StatefulSets are a type of workloads whose data or status is stored while they are running. For example, MySQL is a StatefulSet because it needs to store new data.

A container can be migrated between different hosts, but data is not stored on the hosts. To store StatefulSet data persistently, attach HA storage volumes provided by CCE to the container.

Notes and Constraints
---------------------

-  When you delete or scale a StatefulSet, the system does not delete the storage volumes associated with the StatefulSet to ensure data security.
-  When you delete a StatefulSet, reduce the number of replicas to **0** before deleting the StatefulSet so that pods in the StatefulSet can be stopped in order.
-  When you create a StatefulSet, a headless Service is required for pod access. For details, see :ref:`Headless Service <cce_10_0398>`.
-  When a node is unavailable, pods become **Unready**. In this case, you need to manually delete the pods of the StatefulSet so that the pods can be migrated to a normal node.

Prerequisites
-------------

-  Before creating a workload, you must have an available cluster. For details on how to create a cluster, see :ref:`Creating a CCE Cluster <cce_10_0028>`.
-  To enable public access to a workload, ensure that an EIP or load balancer has been bound to at least one node in the cluster.

   .. note::

      If a pod has multiple containers, ensure that the ports used by the containers do not conflict with each other. Otherwise, creating the StatefulSet will fail.

Using the CCE Console
---------------------

#. Log in to the CCE console.

#. Click the cluster name to access the cluster details page, choose **Workloads** in the navigation pane, and click the **Create Workload** in the upper right corner.

#. Set basic information about the workload.

   **Basic Info**

   -  **Workload Type**: Select **StatefulSet**. For details about workload types, see :ref:`Overview <cce_10_0006>`.
   -  **Workload Name**: Enter the name of the workload. Enter 1 to 52 characters starting with a lowercase letter and ending with a letter or digit. Only lowercase letters, digits, and hyphens (-) are allowed.
   -  **Namespace**: Select the namespace of the workload. The default value is **default**. You can also click **Create Namespace** to create one. For details, see :ref:`Creating a Namespace <cce_10_0278>`.
   -  **Pods**: Enter the number of pods.
   -  **Container Runtime**: A CCE cluster uses runC by default, whereas a CCE Turbo cluster supports both runC and Kata. For details about the differences between runC and Kata, see :ref:`Kata Containers and Common Containers <cce_10_0463>`.
   -  **Time Zone Synchronization**: Specify whether to enable time zone synchronization. After time zone synchronization is enabled, the container and node use the same time zone. The time zone synchronization function depends on the local disk mounted to the container. Do not modify or delete the time zone. For details, see :ref:`Configuring Time Zone Synchronization <cce_10_0354>`.

   **Container Settings**

   -  Container Information

      Multiple containers can be configured in a pod. You can click **Add Container** on the right to configure multiple containers for the pod.

      -  **Basic Info**: See :ref:`Setting Basic Container Information <cce_10_0396>`.
      -  **Lifecycle**: See :ref:`Setting Container Lifecycle Parameters <cce_10_0105>`.
      -  **Health Check**: See :ref:`Setting Health Check for a Container <cce_10_0112>`.
      -  **Environment Variables**: See :ref:`Setting an Environment Variable <cce_10_0113>`.
      -  **Data Storage**: See :ref:`Overview <cce_10_0307>`.

         .. note::

            -  StatefulSets support dynamically provisioned EVS volumes.

               Dynamic mounting is achieved by using the `volumeClaimTemplates <https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/#volume-claim-templates>`__ field and depends on the dynamic creation capability of StorageClass. A StatefulSet associates each pod with a unique PVC using the **volumeClaimTemplates** field, and the PVCs are bound to their corresponding PVs. Therefore, after the pod is rescheduled, the original data can still be mounted thanks to the PVC.

            -  After a workload is created, the storage that is dynamically mounted cannot be updated.

      -  **Security Context**: Set container permissions to protect the system and other containers from being affected. Enter the user ID to set container permissions and prevent systems and other containers from being affected.
      -  **Logging**: See :ref:`Using ICAgent to Collect Container Logs <cce_10_0018>`.

   -  **Image Access Credential**: Select the credential used for accessing the image repository. The default value is **default-secret**. You can use default-secret to access images in SWR. For details about **default-secret**, see :ref:`default-secret <cce_10_0388__section11760122012591>`.

   -  **GPU graphics card**: **All** is selected by default. The workload instance will be scheduled to the node with the specified GPU graphics card type.

   **Headless Service Parameters**

   A headless Service is used to solve the problem of mutual access between pods in a StatefulSet. The headless Service provides a fixed access domain name for each pod. For details, see :ref:`Headless Service <cce_10_0398>`.

   **Service Settings**

   A Service is used for pod access. With a fixed IP address, a Service forwards access traffic to pods and performs load balancing for these pods.

   You can also create a Service after creating a workload. For details about the Service, see :ref:`Service Overview <cce_10_0249>`.

   **Advanced Settings**

   -  **Upgrade**: See :ref:`Configuring the Workload Upgrade Policy <cce_10_0397>`.

   -  **Scheduling**: See :ref:`Scheduling Policy (Affinity/Anti-affinity) <cce_10_0232>`.

   -  **Instances Management Policies**

      For some distributed systems, the StatefulSet sequence is unnecessary and/or should not occur. These systems require only uniqueness and identifiers.

      -  **OrderedReady**: The StatefulSet will deploy, delete, or scale pods in order and one by one. (The StatefulSet continues only after the previous pod is ready or deleted.) This is the default policy.
      -  **Parallel**: The StatefulSet will create pods in parallel to match the desired scale without waiting, and will delete all pods at once.

   -  **Toleration**: Using both taints and tolerations allows (not forcibly) the pod to be scheduled to a node with the matching taints, and controls the pod eviction policies after the node where the pod is located is tainted. For details, see :ref:`Tolerations <cce_10_0352__section2047442210417>`.

   -  **Labels and Annotations**: See :ref:`Pod Labels and Annotations <cce_10_0386>`.

   -  **DNS**: See :ref:`DNS Configuration <cce_10_0365>`.

#. Click **Create Workload** in the lower right corner.

Using kubectl
-------------

In this example, an nginx workload is used and the EVS volume is dynamically mounted to it using the **volumeClaimTemplates** field.

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

#. If the workload will be accessed through a ClusterIP or NodePort Service, set the corresponding workload access type. For details, see :ref:`Networking <cce_10_0020>`.
