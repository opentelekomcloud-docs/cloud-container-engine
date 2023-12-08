:original_name: cce_bestpractice_00253_0.html

.. _cce_bestpractice_00253_0:

Dynamically Creating and Mounting Subdirectories of an SFS Turbo File System
============================================================================

Background
----------

The minimum capacity of an SFS Turbo file system is 500 GiB, and the SFS Turbo file system cannot be billed by usage. By default, the root directory of an SFS Turbo file system is mounted to a container which, in most case, does not require such a large capacity.

The everest add-on allows you to dynamically create subdirectories in an SFS Turbo file system and mount these subdirectories to containers. In this way, an SFS Turbo file system can be shared by multiple containers to increase storage efficiency.

Constraints
-----------

-  Only clusters of v1.15 or later are supported.
-  The cluster must use the everest add-on of version 1.1.13 or later.
-  Kata containers are not supported.
-  When the everest add-on earlier than 1.2.69 or 2.1.11 is used, a maximum of 10 PVCs can be created concurrently at a time by using the subdirectory function. everest of 1.2.69 or later or of 2.1.11 or later is recommended.

Creating an SFS Turbo Volume of the subpath Type
------------------------------------------------

.. caution::

   Do not expand, disassociate, or delete a **subpath** volume.

#. Create an SFS Turbo file system in the same VPC and subnet as the cluster.

#. Create a YAML file of StorageClass, for example, **sfsturbo-subpath-sc.yaml**.

   The following is an example:

   .. code-block::

      apiVersion: storage.k8s.io/v1
      allowVolumeExpansion: true
      kind: StorageClass
      metadata:
        name: sfsturbo-subpath-sc
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

   -  **name**: indicates the name of the StorageClass.
   -  **mountOptions**: indicates the mount options. This field is optional.

      -  In versions later than everest 1.1.13 and earlier than everest 1.2.8, only the **nolock** parameter can be configured. By default, the **nolock** parameter is used for the mount operation and does not need to be configured. If **nolock** is set to **false**, the **lock** field is used.

      -  More options are available in everest 1.2.8 or a later version. For details, see `Setting Mount Options <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_10_0337.html>`__. **Do not set nolock to true. Otherwise, the mount operation will fail.**

         .. code-block::

            mountOptions:
            - vers=3
            - timeo=600
            - nolock
            - hard

   -  **everest.io/volume-as**: This parameter is set to **subpath** to use the **subpath** volume.
   -  **everest.io/share-access-to**: This parameter is optional. In a **subpath** volume, set this parameter to the ID of the VPC where the SFS Turbo file system is located.
   -  **everest.io/share-expand-type**: This parameter is optional. If the type of the SFS Turbo file system is **SFS Turbo Standard - Enhanced** or **SFS Turbo Performance - Enhanced**, set this parameter to **bandwidth**.
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
        name: sfs-turbo-test
        namespace: default
      spec:
        accessModes:
        - ReadWriteMany
        resources:
          requests:
            storage: 50Gi
        storageClassName: sfsturbo-subpath-sc
        volumeMode: Filesystem

   In this example:

   -  **name**: indicates the name of the PVC.
   -  **storageClassName**: specifies the name of the StorageClass.
   -  **storage**: In the subpath mode, it is useless to specify this parameter. The storage capacity is limited by the total capacity of the SFS Turbo file system. If the total capacity of the SFS Turbo file system is insufficient, expand the capacity on the SFS Turbo page in a timely manner.

5. Run **kubectl create -f sfs-turbo-test.yaml**.

.. note::

   It is meaningless to conduct capacity expansion on an SFS Turbo volume created in the subpath mode. This operation does not expand the capacity of the SFS Turbo file system. Ensure that the total capacity of the SFS Turbo file system is not used up.

Creating a Deployment and Mounting an Existing Volume to the Deployment
-----------------------------------------------------------------------

#. Create a YAML file for the Deployment, for example, **deployment-test.yaml**.

   The following is an example:

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

   -  **name**: indicates the name of the Deployment.
   -  **image**: specifies the image used by the Deployment.
   -  **mountPath**: indicates the mount path of the container. In this example, the volume is mounted to the **/tmp** directory.
   -  **claimName**: indicates the name of an existing PVC.

2. Create the Deployment.

   **kubectl create -f deployment-test.yaml**

Dynamically Creating a subpath Volume for a StatefulSet
-------------------------------------------------------

#. Create a YAML file for a StatefulSet, for example, **statefulset-test.yaml**.

   The following is an example:

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
              storageClassName: sfsturbo-subpath-sc
        serviceName: wwww
        podManagementPolicy: OrderedReady
        updateStrategy:
          type: RollingUpdate
        revisionHistoryLimit: 10

   In this example:

   -  **name**: indicates the name of the StatefulSet.
   -  **image**: specifies the image used by the StatefulSet.
   -  **mountPath**: indicates the mount path of the container. In this example, the volume is mounted to the **/tmp** directory.
   -  **spec.template.spec.containers.volumeMounts.name** and **spec.volumeClaimTemplates.metadata.name**: must be consistent because they are mapped to each other.
   -  **storageClassName**: indicates the name of the StorageClass.

2. Create the StatefulSet.

   **kubectl create -f statefulset-test.yaml**
