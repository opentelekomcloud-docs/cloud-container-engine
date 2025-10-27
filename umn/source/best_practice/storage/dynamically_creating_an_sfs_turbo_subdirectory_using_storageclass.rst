:original_name: cce_bestpractice_00253.html

.. _cce_bestpractice_00253:

Dynamically Creating an SFS Turbo Subdirectory Using StorageClass
=================================================================

Background
----------

The minimum capacity of an SFS Turbo file system is 1000 GiB. By default, the root directory of an SFS Turbo file system is mounted to a container which, in most case, does not require such a large capacity.

The Everest add-on allows you to dynamically create subdirectories in an SFS Turbo file system and mount these subdirectories to containers. In this way, an SFS Turbo file system can be shared by multiple containers to increase storage efficiency.

Notes and Constraints
---------------------

-  Only clusters of v1.15 or later are supported.

-  The cluster must use the Everest add-on of version 1.1.13 or later.

-  Kata containers are not supported.

-  When the Everest add-on earlier than 1.2.69 or 2.1.11 is used, a maximum of 10 PVCs can be created concurrently at a time by using the subdirectory function. everest of 1.2.69 or later or of 2.1.11 or later is recommended.

-  A subPath volume is a subdirectory of an SFS Turbo file system. Increasing the capacity of a PVC of this type only changes the resource range specified by the PVC, but does not change the total capacity of the SFS Turbo file system. If the SFS Turbo file system's total resource capacity is not enough, the available capacity of the subPath volume will be restricted. To fix this, you must increase the resource capacity of the SFS Turbo file system on the SFS Turbo console.

   Deleting the subPath volume does not result in the deletion of the resources of the SFS Turbo file system.

Creating an SFS Turbo Volume of the subPath Type
------------------------------------------------

#. Create an SFS Turbo file system in the same VPC and subnet as the cluster.

#. Create a YAML file of StorageClass, for example, **sfsturbo-subpath-sc.yaml**.

   The following is an example:

   .. code-block::

      apiVersion: storage.k8s.io/v1
      allowVolumeExpansion: true
      kind: StorageClass
      metadata:
        name: sfsturbo-subpath-sc # Storage class name
      mountOptions:  #Mount options
      - lock
      parameters:
        csi.storage.k8s.io/csi-driver-name: sfsturbo.csi.everest.io
        csi.storage.k8s.io/fstype: nfs
        everest.io/archive-on-delete: "true"
        everest.io/share-access-to: 7ca2dba2-1234-1234-1234-626371a8fb3a
        everest.io/share-expand-type: bandwidth
        everest.io/share-export-location: 192.168.1.1:/sfsturbo/  # Mount directory configuration
        everest.io/share-source: sfs-turbo
        everest.io/share-volume-type: STANDARD
        everest.io/volume-as: subpath
        everest.io/volume-id: 0d773f2e-1234-1234-1234-de6a35074696  # ID of an SFS Turbo volume
      provisioner: everest-csi-provisioner
      reclaimPolicy: Delete
      volumeBindingMode: Immediate

   In this example:

   -  **name**: indicates the name of the StorageClass.
   -  **mountOptions**: (Optional) indicates the mount options.

      -  In versions later than Everest 1.1.13 and earlier than Everest 1.2.8, only the **nolock** parameter can be configured. By default, the **nolock** parameter is used for the mount operation and does not need to be configured. If **nolock** is set to **false**, the **lock** field is used.

      -  Starting from Everest 1.2.8, more mount options are supported. For details, see `Configuring SFS Volume Mount Options <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_10_0337.html>`__. **Do not set nolock to true. Otherwise, the mount operation will fail.**

         .. code-block::

            mountOptions:
            - vers=3
            - timeo=600
            - nolock
            - hard

   -  **everest.io/volume-as**: This parameter is set to **subpath** to use the subPath volume.
   -  **everest.io/share-access-to**: This parameter is optional. In a subPath volume, set this parameter to the ID of the VPC where the SFS Turbo file system is located.
   -  **everest.io/share-expand-type**: This parameter is optional. If the type of the SFS Turbo file system is SFS Turbo Standard - Enhanced or SFS Turbo Performance - Enhanced, set this parameter to **bandwidth**.
   -  **everest.io/share-export-location**: This parameter indicates the mount directory. It consists of the SFS Turbo shared path and sub-directory. The shared path can be obtained on the SFS Turbo console. The sub-directory is user-defined. The PVCs created using the StorageClass are located in this sub-directory.
   -  **everest.io/share-volume-type**: This parameter is optional. It specifies the SFS Turbo file system type. The value can be **STANDARD** or **PERFORMANCE**. For enhanced types, this parameter must be used together with **everest.io/share-expand-type** (whose value should be **bandwidth**).
   -  **everest.io/zone**: This parameter is optional. Set it to the AZ where the SFS Turbo file system is located.
   -  **everest.io/volume-id**: This parameter indicates the ID of the SFS Turbo volume. You can obtain the volume ID on the SFS Turbo page.
   -  **everest.io/archive-on-delete**: If this parameter is set to **true** and **Delete** is selected for **Reclaim Policy**, the original documents of the PV will be archived to the directory named **archived-**\ *{$PV name.timestamp}* before the PVC is deleted. If this parameter is set to **false**, the SFS Turbo subdirectory of the corresponding PV will be deleted. The default value is **true**, indicating that the original documents of the PV will be archived to the directory named **archived-**\ *{$PV name.timestamp}* before the PVC is deleted.

3. Run **kubectl create -f sfsturbo-subpath-sc.yaml**.

4. Create a PVC YAML file named **sfs-turbo-test.yaml**.

   The following is an example:

   .. code-block::

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        name: sfs-turbo-test  # PVC name
        namespace: default
      spec:
        accessModes:
        - ReadWriteMany
        resources:
          requests:
            storage: 50Gi
        storageClassName: sfsturbo-subpath-sc  # Storage class name
        volumeMode: Filesystem

   In this example:

   -  **name**: indicates the name of the PVC.
   -  **storageClassName**: specifies the name of the StorageClass.
   -  **storage**: In a subPath volume, modifying the value of this parameter does not impact the resource capacity of the SFS Turbo file system. A subPath volume is essentially a file path within an SFS Turbo file system. As a result, increasing the capacity of the subPath volume in a PVC does not lead to an increase in the resources of the SFS Turbo file system.

      .. note::

         The capacity of a subPath volume is restricted by the overall resource capacity of the corresponding SFS Turbo file system. If the resources of the SFS Turbo file system are inadequate, you can adjust the resource capacity via the SFS Turbo console.

5. Run **kubectl create -f sfs-turbo-test.yaml**.

Creating a Deployment and Mounting an Existing Volume
-----------------------------------------------------

#. Create a YAML file for the Deployment, for example, **deployment-test.yaml**.

   The following is an example:

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: test-turbo-subpath-example   # Name of the created workload
        namespace: default
        generation: 1
        labels:
          appgroup: ''
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: test-turbo-subpath-example
        template:
          metadata:
            labels:
              app: test-turbo-subpath-example
          spec:
            containers:
            - image: nginx:latest  # Image of the workload
              name: container-0
              volumeMounts:
              - mountPath: /tmp   #Mount path in a container
                name: pvc-sfs-turbo-example
            restartPolicy: Always
            imagePullSecrets:
            - name: default-secret
            volumes:
            - name: pvc-sfs-turbo-example
              persistentVolumeClaim:
                claimName: sfs-turbo-test   # Name of an existing PVC

   In this example:

   -  **name**: indicates the name of the created workload.
   -  **image**: specifies the image used by the workload.
   -  **mountPath**: indicates the mount path of the container. In this example, the volume is mounted to the **/tmp** directory.
   -  **claimName**: indicates the name of an existing PVC.

2. Create the Deployment.

   **kubectl create -f deployment-test.yaml**

Dynamically Creating a subPath Volume for a StatefulSet
-------------------------------------------------------

#. Create a YAML file for a StatefulSet, for example, **statefulset-test.yaml**.

   The following is an example:

   .. code-block::

      apiVersion: apps/v1
      kind: StatefulSet
      metadata:
        name: test-turbo-subpath # Name of the created workload
        namespace: default
        generation: 1
        labels:
          appgroup: ''
      spec:
        replicas: 2
        selector:
          matchLabels:
            app: test-turbo-subpath
        template:
          metadata:
            labels:
              app: test-turbo-subpath
            annotations:
              metrics.alpha.kubernetes.io/custom-endpoints: '[{"api":"","path":"","port":"","names":""}]'
              pod.alpha.kubernetes.io/initialized: 'true'
          spec:
            containers:
              - name: container-0
                image: 'nginx:latest'  # Image of the workload
                resources: {}
                volumeMounts:
                  - name: sfs-turbo-160024548582479676
                    mountPath: /tmp  # Mount path in a container
                terminationMessagePath: /dev/termination-log
                terminationMessagePolicy: File
                imagePullPolicy: IfNotPresent
            restartPolicy: Always
            terminationGracePeriodSeconds: 30
            dnsPolicy: ClusterFirst
            securityContext: {}
            imagePullSecrets:
              - name: default-secret
            affinity: {}
            schedulerName: default-scheduler
        volumeClaimTemplates:
          - metadata:
              name: sfs-turbo-160024548582479676
              namespace: default
              annotations: {}
            spec:
              accessModes:
                - ReadWriteMany
              resources:
                requests:
                  storage: 10Gi
              storageClassName: sfsturbo-subpath-sc # Enter the name of a self-managed storage class.
        serviceName: wwww
        podManagementPolicy: OrderedReady
        updateStrategy:
          type: RollingUpdate
        revisionHistoryLimit: 10

   In this example:

   -  **name**: indicates the name of the created workload.
   -  **image**: specifies the image used by the workload.
   -  **mountPath**: indicates the mount path of the container. In this example, the volume is mounted to the **/tmp** directory.
   -  **spec.template.spec.containers.volumeMounts.name** and **spec.volumeClaimTemplates.metadata.name**: must be consistent because they have a mapping relationship.
   -  **storageClassName**: specifies the name of an on-premises StorageClass.

2. Create the StatefulSet.

   **kubectl create -f statefulset-test.yaml**
