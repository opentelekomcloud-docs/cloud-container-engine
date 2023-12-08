:original_name: cce_bestpractice_00281.html

.. _cce_bestpractice_00281:

Custom Storage Classes
======================

Background
----------

When using storage resources in CCE, the most common method is to specify **storageClassName** to define the type of storage resources to be created when creating a PVC. The following configuration shows how to use a PVC to apply for an SAS (high I/O) EVS disk (block storage).

.. code-block::

   apiVersion: v1
   kind: PersistentVolumeClaim
   metadata:
     name: pvc-evs-example
     namespace: default
     annotations:
       everest.io/disk-volume-type: SAS
   spec:
     accessModes:
     - ReadWriteOnce
     resources:
       requests:
         storage: 10Gi
     storageClassName: csi-disk

To specify the EVS disk type, you can set the **everest.io/disk-volume-type** field. The value **SAS** is used as an example here, indicating the high I/O EVS disk type. Or you can choose **SATA** (common I/O) and **SSD** (ultra-high I/O).

This configuration method may not work if you want to:

-  Set **storageClassName** only, which is simpler than specifying the EVS disk type by using **everest.io/disk-volume-type**.
-  Avoid modifying YAML files or Helm charts. Some users switch from self-built or other Kubernetes services to CCE and have written YAML files of many applications. In these YAML files, different types of storage resources are specified by different StorageClassNames. When using CCE, they need to modify a large number of YAML files or Helm charts to use storage resources, which is labor-consuming and error-prone.
-  Set the default **storageClassName** for all applications to use the default storage class. In this way, you can create storage resources of the default type without needing to specify **storageClassName** in the YAML file.

Solution
--------

This section describes how to set a custom storage class in CCE and how to set the default storage class. You can specify different types of storage resources by setting **storageClassName**.

-  For the first scenario, you can define custom storageClassNames for SAS and SSD EVS disks. For example, define a storage class named **csi-disk-sas** for creating SAS disks. The following figure shows the differences before and after you use a custom storage class.

   |image1|

-  For the second scenario, you can define a storage class with the same name as that in the existing YAML file without needing to modify **storageClassName** in the YAML file.

-  For the third scenario, you can set the default storage class as described below to create storage resources without specifying **storageClassName** in YAML files.

   .. code-block::

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        name: pvc-evs-example
        namespace: default
      spec:
        accessModes:
        - ReadWriteOnce
        resources:
          requests:
            storage: 10Gi

Default Storage Classes in CCE
------------------------------

Run the following command to query the supported storage classes.

.. code-block::

   # kubectl get sc
   NAME                PROVISIONER                     AGE
   csi-disk            everest-csi-provisioner         17d          # Storage class for EVS disks
   csi-disk-topology   everest-csi-provisioner         17d          # Storage class for EVS disks with delayed association
   csi-nas             everest-csi-provisioner         17d          # Storage class for SFS file systems
   csi-obs             everest-csi-provisioner         17d          # Storage Class for OBS buckets
   csi-sfsturbo        everest-csi-provisioner         17d          # Storage class for SFS Turbo file systems

Check the details of **csi-disk**. You can see that the type of the disk created by **csi-disk** is SAS by default.

.. code-block::

   # kubectl get sc csi-disk -oyaml
   allowVolumeExpansion: true
   apiVersion: storage.k8s.io/v1
   kind: StorageClass
   metadata:
     creationTimestamp: "2021-03-17T02:10:32Z"
     name: csi-disk
     resourceVersion: "760"
     selfLink: /apis/storage.k8s.io/v1/storageclasses/csi-disk
     uid: 4db97b6c-853b-443d-b0dc-41cdcb8140f2
   parameters:
     csi.storage.k8s.io/csi-driver-name: disk.csi.everest.io
     csi.storage.k8s.io/fstype: ext4
     everest.io/disk-volume-type: SAS
     everest.io/passthrough: "true"
   provisioner: everest-csi-provisioner
   reclaimPolicy: Delete
   volumeBindingMode: Immediate


Custom Storage Classes
----------------------

You can customize a high I/O storage class in a YAML file. For example, the name **csi-disk-sas** indicates that the disk type is SAS (high I/O).

.. code-block::

   apiVersion: storage.k8s.io/v1
   kind: StorageClass
   metadata:
     name: csi-disk-sas                          # Name of the high I/O storage class, which can be customized.
   parameters:
     csi.storage.k8s.io/csi-driver-name: disk.csi.everest.io
     csi.storage.k8s.io/fstype: ext4
     everest.io/disk-volume-type: SAS            # High I/O EVS disk type, which cannot be customized.
     everest.io/passthrough: "true"
   provisioner: everest-csi-provisioner
   reclaimPolicy: Delete
   volumeBindingMode: Immediate
   allowVolumeExpansion: true                    # true indicates that capacity expansion is allowed.

For an ultra-high I/O storage class, you can set the class name to **csi-disk-ssd** to create SSD EVS disk (ultra-high I/O).

.. code-block::

   apiVersion: storage.k8s.io/v1
   kind: StorageClass
   metadata:
     name: csi-disk-ssd                       # Name of the ultra-high I/O storage class, which can be customized.
   parameters:
     csi.storage.k8s.io/csi-driver-name: disk.csi.everest.io
     csi.storage.k8s.io/fstype: ext4
     everest.io/disk-volume-type: SSD         # Ultra-high I/O EVS disk type, which cannot be customized.
     everest.io/passthrough: "true"
   provisioner: everest-csi-provisioner
   reclaimPolicy: Delete
   volumeBindingMode: Immediate
   allowVolumeExpansion: true

**reclaimPolicy**: indicates the recycling policies of the underlying cloud storage. The value can be **Delete** or **Retain**.

-  **Delete**: When a PVC is deleted, both the PV and the EVS disk are deleted.
-  **Retain**: When a PVC is deleted, the PV and underlying storage resources are not deleted. Instead, you must manually delete these resources. After that, the PV resource is in the **Released** state and cannot be bound to the PVC again.

.. note::

   The reclamation policy set here has no impact on the SFS Turbo storage.

If high data security is required, you are advised to select **Retain** to prevent data from being deleted by mistake.

After the definition is complete, run the **kubectl create** commands to create storage resources.

.. code-block::

   # kubectl create -f sas.yaml
   storageclass.storage.k8s.io/csi-disk-sas created
   # kubectl create -f ssd.yaml
   storageclass.storage.k8s.io/csi-disk-ssd created

Query the storage class again. Two more types of storage classes are displayed in the command output, as shown below.

.. code-block::

   # kubectl get sc
   NAME                PROVISIONER                     AGE
   csi-disk            everest-csi-provisioner         17d
   csi-disk-sas        everest-csi-provisioner         2m28s
   csi-disk-ssd        everest-csi-provisioner         16s
   csi-disk-topology   everest-csi-provisioner         17d
   csi-nas             everest-csi-provisioner         17d
   csi-obs             everest-csi-provisioner         17d
   csi-sfsturbo        everest-csi-provisioner         17d

Other types of storage resources can be defined in the similar way. You can use kubectl to obtain the YAML file and modify it as required.

-  File storage

   .. code-block::

      # kubectl get sc csi-nas -oyaml
      kind: StorageClass
      apiVersion: storage.k8s.io/v1
      metadata:
        name: csi-nas
      provisioner: everest-csi-provisioner
      parameters:
        csi.storage.k8s.io/csi-driver-name: nas.csi.everest.io
        csi.storage.k8s.io/fstype: nfs
        everest.io/share-access-level: rw
        everest.io/share-access-to: 5e3864c6-e78d-4d00-b6fd-de09d432c632   # ID of the VPC to which the cluster belongs
        everest.io/share-is-public: 'false'
        everest.io/zone: xxxxx          # AZ
      reclaimPolicy: Delete
      allowVolumeExpansion: true
      volumeBindingMode: Immediate

-  Object storage

   .. code-block::

      # kubectl get sc csi-obs -oyaml
      kind: StorageClass
      apiVersion: storage.k8s.io/v1
      metadata:
        name: csi-obs
      provisioner: everest-csi-provisioner
      parameters:
        csi.storage.k8s.io/csi-driver-name: obs.csi.everest.io
        csi.storage.k8s.io/fstype: s3fs           # Object storage type. s3fs indicates an object bucket, and obsfs indicates a parallel file system.
        everest.io/obs-volume-type: STANDARD      # Storage class of the OBS bucket
      reclaimPolicy: Delete
      volumeBindingMode: Immediate

Specifying a Default StorageClass
---------------------------------

You can specify a storage class as the default class. In this way, if you do not specify **storageClassName** when creating a PVC, the PVC is created using the default storage class.

For example, to specify **csi-disk-ssd** as the default storage class, edit your YAML file as follows:

.. code-block::

   apiVersion: storage.k8s.io/v1
   kind: StorageClass
   metadata:
     name: csi-disk-ssd
     annotations:
       storageclass.kubernetes.io/is-default-class: "true"   # Specifies the default storage class in a cluster. A cluster can have only one default storage class.
   parameters:
     csi.storage.k8s.io/csi-driver-name: disk.csi.everest.io
     csi.storage.k8s.io/fstype: ext4
     everest.io/disk-volume-type: SSD
     everest.io/passthrough: "true"
   provisioner: everest-csi-provisioner
   reclaimPolicy: Delete
   volumeBindingMode: Immediate
   allowVolumeExpansion: true

Delete the created csi-disk-ssd disk, run the **kubectl create** command to create a csi-disk-ssd disk again, and then query the storage class. The following information is displayed.

.. code-block::

   # kubectl delete sc csi-disk-ssd
   storageclass.storage.k8s.io "csi-disk-ssd" deleted
   # kubectl create -f ssd.yaml
   storageclass.storage.k8s.io/csi-disk-ssd created
   # kubectl get sc
   NAME                     PROVISIONER                     AGE
   csi-disk                 everest-csi-provisioner         17d
   csi-disk-sas             everest-csi-provisioner         114m
   csi-disk-ssd (default)   everest-csi-provisioner         9s
   csi-disk-topology        everest-csi-provisioner         17d
   csi-nas                  everest-csi-provisioner         17d
   csi-obs                  everest-csi-provisioner         17d
   csi-sfsturbo             everest-csi-provisioner         17d

Verification
------------

-  Use **csi-disk-sas** to create a PVC.

   .. code-block::

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        name:  sas-disk
      spec:
        accessModes:
        - ReadWriteOnce
        resources:
          requests:
            storage: 10Gi
        storageClassName: csi-disk-sas

   Create a storage class and view its details. As shown below, the object can be created and the value of **STORAGECLASS** is **csi-disk-sas**.

   .. code-block::

      # kubectl create -f sas-disk.yaml
      persistentvolumeclaim/sas-disk created
      # kubectl get pvc
      NAME       STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
      sas-disk   Bound    pvc-6e2f37f9-7346-4419-82f7-b42e79f7964c   10Gi       RWO            csi-disk-sas   24s
      # kubectl get pv
      NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM                     STORAGECLASS   REASON   AGE
      pvc-6e2f37f9-7346-4419-82f7-b42e79f7964c   10Gi       RWO            Delete           Bound       default/sas-disk          csi-disk-sas            30s

   View the PVC details on the CCE console. On the PV details page, you can see that the disk type is high I/O.

-  If **storageClassName** is not specified, the default configuration is used, as shown below.

   .. code-block::

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        name:  ssd-disk
      spec:
        accessModes:
        - ReadWriteOnce
        resources:
          requests:
            storage: 10Gi

   Create and view the storage resource. You can see that the storage class of PVC ssd-disk is csi-disk-ssd, indicating that csi-disk-ssd is used by default.

   .. code-block::

      # kubectl create -f ssd-disk.yaml
      persistentvolumeclaim/ssd-disk created
      # kubectl get pvc
      NAME       STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
      sas-disk   Bound    pvc-6e2f37f9-7346-4419-82f7-b42e79f7964c   10Gi       RWO            csi-disk-sas   16m
      ssd-disk   Bound    pvc-4d2b059c-0d6c-44af-9994-f74d01c78731   10Gi       RWO            csi-disk-ssd   10s
      # kubectl get pv
      NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM                     STORAGECLASS   REASON   AGE
      pvc-4d2b059c-0d6c-44af-9994-f74d01c78731   10Gi       RWO            Delete           Bound       default/ssd-disk          csi-disk-ssd            15s
      pvc-6e2f37f9-7346-4419-82f7-b42e79f7964c   10Gi       RWO            Delete           Bound       default/sas-disk          csi-disk-sas            17m

   View the PVC details on the CCE console. On the PV details page, you can see that the disk type is ultra-high I/O.

.. |image1| image:: /_static/images/en-us_image_0000001653425692.png
