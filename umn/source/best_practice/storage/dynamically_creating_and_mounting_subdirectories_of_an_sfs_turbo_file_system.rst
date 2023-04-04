:original_name: cce_bestpractice_00253_0.html

.. _cce_bestpractice_00253_0:

Dynamically Creating and Mounting Subdirectories of an SFS Turbo File System
============================================================================

Background
----------

The minimum capacity of an SFS Turbo file system is 500 GB, and the SFS Turbo file system cannot be billed by usage. By default, the root directory of an SFS Turbo file system is mounted to a container which, in most case, does not require such a large capacity.

The everest add-on allows you to dynamically create subdirectories in an SFS Turbo file system and mount these subdirectories to containers. In this way, an SFS Turbo file system can be shared by multiple containers to increase storage efficiency.

Notes and Constraints
---------------------

-  Only clusters of v1.15 and later are supported.
-  The cluster must use the everest add-on of version 1.1.13 or later.
-  Kata containers are not supported.
-  A maximum of 10 PVCs can be created concurrently at a time by using the subdirectory function.

Creating an SFS Turbo Volume of the subpath Type
------------------------------------------------

.. caution::

   The CCE console has not yet supported the operations related to this feature, such as expanding, disassociating, and deleting subPath volumes.

#. Import an SFS Turbo file system that is located in the same VPC and subnet as the cluster.

#. Create a StorageClass YAML file, for example, **sfsturbo-sc-test.yaml**.

   Configuration example:

   .. code-block::

      apiVersion: storage.k8s.io/v1
      allowVolumeExpansion: true
      kind: StorageClass
      metadata:
        name: sfsturbo-sc-test
      mountOptions:
      - lock
      parameters:
        csi.storage.k8s.io/csi-driver-name: sfsturbo.csi.everest.io
        csi.storage.k8s.io/fstype: nfs
        everest.io/archive-on-delete: "true"
        everest.io/share-access-to: 7ca2dba2-1234-1234-1234-626371a8fb3a
        everest.io/share-expand-type: bandwidth
        everest.io/share-export-location: 192.168.1.1:/sfsturbo/
        everest.io/share-source: sfs-turbo
        everest.io/share-volume-type: STANDARD
        everest.io/volume-as: subpath
        everest.io/volume-id: 0d773f2e-1234-1234-1234-de6a35074696
      provisioner: everest-csi-provisioner
      reclaimPolicy: Delete
      volumeBindingMode: Immediate

   In this example:

   -  **name**: name of the StorageClass.
   -  **mountOptions**: mount options. This field is optional.

      -  In versions later than everest 1.1.13 and earlier than everest 1.2.8, only the **nolock** parameter can be configured. By default, the **nolock** parameter is used for the mount operation and does not need to be configured. If **nolock** is set to **false**, the **lock** field is used.

      -  Starting from everest 1.2.8, more parameters are supported. The default parameter configurations are shown below. For details, see `Setting Mount Options <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_01_0337.html>`__. **Do not set nolock to true. Otherwise, the mount operation fails.**

         .. code-block::

            mountOptions:
            - vers=3
            - timeo=600
            - nolock
            - hard

   -  **everest.io/volume-as**: Set this parameter to **subpath**.
   -  **everest.io/share-access-to**: This parameter is optional. In subpath mode, set this parameter to the ID of the VPC where the SFS Turbo file system is located.
   -  **everest.io/share-expand-type**: This parameter is optional. If the type of the SFS Turbo file system is SFS Turbo Standard - Enhanced or SFS Turbo Performance - Enhanced, set this parameter to **bandwidth**.
   -  **everest.io/share-export-location**: root directory to be mounted. It consists of the SFS Turbo shared path and sub-directory. The shared path can be queried on the SFS Turbo console. The sub-directory is user-defined. The PVCs created by the StorageClass are located in the sub-directory.
   -  **everest.io/share-volume-type**: This parameter is optional. It specifies the SFS Turbo file system type. The value can be **STANDARD** or **PERFORMANCE**. For enhanced types, this parameter must be used together with **everest.io/share-expand-type** (whose value should be **bandwidth**).
   -  **everest.io/zone**: This parameter is optional. Set it to the AZ where the SFS Turbo file system is located.
   -  **everest.io/volume-id**: ID of the SFS Turbo volume. You can query the volume ID on the SFS Turbo page.
   -  **everest.io/archive-on-delete**: If this parameter is set to **true** and the recycling policy is set to **Delete**, the original PV file will be archived when the PVC is deleted. The archive directory is named in the format of *archived-$PV name.timestamp*. If this parameter is set to **false**, the SFS Turbo sub-directory corresponding to the PV will be deleted. The default value is **true**.

3. Run the **kubectl create -f sfsturbo-sc-test.yaml** command to create a StorageClass.

4. Create a PVC YAML file named **sfs-turbo-test.yaml**.

   Configuration example:

   .. code-block::

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        name: sfs-turbo-test
        namespace: default
      spec:
        accessModes:
        - ReadWriteMany
        resources:
          requests:
            storage: 50Gi
        storageClassName: sfsturbo-sc-test
        volumeMode: Filesystem

   In this example:

   -  **name**: name of the PVC.
   -  **storageClassName**: name of the StorageClass created in the previous step.
   -  **storage**: In the subpath mode, this parameter is invalid. The storage capacity is limited by the total capacity of the SFS Turbo file system. If the total capacity of the SFS Turbo file system is insufficient, expand the capacity on the SFS Turbo page in a timely manner.

5. Run the **kubectl create -f sfs-turbo-test.yaml** command to create a PVC.

.. note::

   It is meaningless to conduct capacity expansion on an SFS Turbo volume created in the subpath mode. This operation does not expand the capacity of the SFS Turbo file system. You need to ensure that the total capacity of the SFS Turbo file system is not used up.

Creating a Deployment and Mounting an Existing Volume
-----------------------------------------------------

#. Create a Deployment YAML file named **deployment-test.yaml**.

   Configuration example:

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: test-turbo-subpath-example
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
            - image: nginx:latest
              name: container-0
              volumeMounts:
              - mountPath: /tmp
                name: pvc-sfs-turbo-example
            restartPolicy: Always
            imagePullSecrets:
            - name: default-secret
            volumes:
            - name: pvc-sfs-turbo-example
              persistentVolumeClaim:
                claimName: sfs-turbo-test

   In this example:

   -  **name**: name of the Deployment.
   -  **image**: image used by the Deployment.
   -  **mountPath**: mount path of the container. In this example, the volume is mounted to the **/tmp** directory.
   -  **claimName**: name of an existing PVC.

2. Run the **kubectl create -f deployment-test.yaml** command to create a Deployment.

Creating a StatefulSet That Uses a Volume Dynamically Created in subpath Mode
-----------------------------------------------------------------------------

#. Create a StatefulSet YAML file named **statefulset-test.yaml**.

   Configuration example:

   .. code-block::

      apiVersion: apps/v1
      kind: StatefulSet
      metadata:
        name: test-turbo-subpath
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
                image: 'nginx:latest'
                env:
                  - name: PAAS_APP_NAME
                    value: deploy-sfs-nfs-rw-in
                  - name: PAAS_NAMESPACE
                    value: default
                  - name: PAAS_PROJECT_ID
                    value: 8190a2a1692c46f284585c56fc0e2fb9
                resources: {}
                volumeMounts:
                  - name: sfs-turbo-160024548582479676
                    mountPath: /tmp
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
                - ReadWriteOnce
              resources:
                requests:
                  storage: 10Gi
              storageClassName: sfsturbo-sc-test
        serviceName: wwww
        podManagementPolicy: OrderedReady
        updateStrategy:
          type: RollingUpdate
        revisionHistoryLimit: 10

   In this example:

   -  **name**: name of the StatefulSet.
   -  **image**: image used by the StatefulSet.
   -  **mountPath**: mount path of the container. In this example, the volume is mounted to the **/tmp** directory.
   -  **spec.template.spec.containers.volumeMounts.name** and **spec.volumeClaimTemplates.metadata.name** must be consistent because they have a mapping relationship.
   -  **storageClassName**: name of the created StorageClass.

2. Run the **kubectl create -f statefulset-test.yaml** command to create a StatefulSet.
