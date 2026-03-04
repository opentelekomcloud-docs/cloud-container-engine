:original_name: cce_bestpractice_00281.html

.. _cce_bestpractice_00281:

Using Custom Storage Classes
============================

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

To specify the EVS disk type, you can configure the **everest.io/disk-volume-type** field. The value **SAS** is used as an example here, indicating the high I/O EVS disk type. Or you can choose **SATA** (common I/O) and **SSD** (ultra-high I/O).

This configuration method may not work if you want to:

-  Set **storageClassName** only, which is simpler than specifying the EVS disk type by using **everest.io/disk-volume-type**.
-  Avoid modifying YAML files or Helm charts. Some users switch from self-built or other Kubernetes services to CCE and have written YAML files of many applications. In these YAML files, different types of storage resources are specified by different StorageClassNames. When using CCE, they need to modify a large number of YAML files or Helm charts to use storage resources, which is labor-consuming and error-prone.
-  Set the default **storageClassName** for all applications to use the default storage class. In this way, you can create storage resources of the default type without needing to specify **storageClassName** in the YAML file.

Solution
--------

This section describes how to set a custom storage class in CCE and how to set the default storage class. You can specify different types of storage resources by setting **storageClassName**.

-  For the first scenario, you can define custom storageClassNames for SAS and SSD EVS disks. For example, define a storage class named **csi-disk-sas** for creating SAS disks. The figure below shows the differences before and after you use a custom storage class. When writing a YAML file, you only need to specify **StorageClassName**. This meets the usage habits of specific users.

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

Creating a StorageClass Using a YAML File
-----------------------------------------

As of now, CCE provides StorageClasses such as csi-disk, csi-nas, and csi-obs by default. When defining a PVC, you can use a **StorageClassName** to automatically create a PV of the corresponding type and automatically create underlying storage resources.

Run the following kubectl command to obtain the StorageClasses that CCE supports. Use the CSI add-on provided by CCE to create a StorageClass.

.. code-block::

   # kubectl get sc
   NAME                PROVISIONER                     AGE
   csi-disk            everest-csi-provisioner         17d          # EVS disk
   csi-disk-topology   everest-csi-provisioner         17d          # EVS disks created with a delay
   csi-nas             everest-csi-provisioner         17d          # SFS 1.0
   csi-sfs             everest-csi-provisioner         17d          # A general purpose file system (SFS 3.0 Capacity-Oriented)
   csi-obs             everest-csi-provisioner         17d          # OBS
   csi-sfsturbo        everest-csi-provisioner         17d          # SFS Turbo

Each StorageClass contains the default parameters used for dynamically creating a PV. The following is an example of StorageClass for EVS disks:

.. code-block::

   kind: StorageClass
   apiVersion: storage.k8s.io/v1
   metadata:
     name: csi-disk
   provisioner: everest-csi-provisioner
   parameters:
     csi.storage.k8s.io/csi-driver-name: disk.csi.everest.io
     csi.storage.k8s.io/fstype: ext4    # (Optional) The file system is of the xfs or ext4 type. If it is left blank, ext4 will be used by default.
     everest.io/disk-volume-type: SAS
     everest.io/passthrough: 'true'
   reclaimPolicy: Delete
   allowVolumeExpansion: true
   volumeBindingMode: Immediate

.. table:: **Table 1** Key parameters

   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                           |
   +===================================+=======================================================================================================================================================================================================================================================================================================================================================+
   | provisioner                       | Specifies the storage resource provider, which is the Everest add-on for CCE. Set this parameter to **everest-csi-provisioner**.                                                                                                                                                                                                                      |
   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | parameters                        | Specifies the storage parameters, which vary with StorageClasses. For details, see :ref:`Table 2 <cce_bestpractice_00281__cce_10_1068_table15415188175413>`.                                                                                                                                                                                          |
   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | reclaimPolicy                     | Specifies the value of **persistentVolumeReclaimPolicy** for creating a PV. The value can be **Delete** or **Retain**. If **reclaimPolicy** is not specified when a StorageClass object is created, the value defaults to **Delete**.                                                                                                                 |
   |                                   |                                                                                                                                                                                                                                                                                                                                                       |
   |                                   | -  **Delete**: When a PVC is deleted, its associated underlying storage resources will be deleted and the PV resources will be removed. Exercise caution if you select this option.                                                                                                                                                                   |
   |                                   | -  **Retain**: When a PVC is deleted, both of the PV and its associated underlying storage resources will be retained and the PV is marked as released. If you manually delete the PV afterwards, the underlying storage resources will not be deleted. To bind the PV to a new PVC, you need to remove the original binding information from the PV. |
   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | allowVolumeExpansion              | Specifies whether the PV of this StorageClass supports dynamic capacity expansion. The default value is **false**. Dynamic capacity expansion is implemented by the underlying storage add-on. This is only a switch.                                                                                                                                 |
   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | volumeBindingMode                 | Specifies the volume binding mode, which is the time when a PV is dynamically created. The value can be **Immediate** or **WaitForFirstConsumer**.                                                                                                                                                                                                    |
   |                                   |                                                                                                                                                                                                                                                                                                                                                       |
   |                                   | -  **Immediate**: After a PVC is created, the storage resources and PV will be created and associated with the PVC without delay.                                                                                                                                                                                                                     |
   |                                   | -  **WaitForFirstConsumer**: After a PVC is created, it will not be immediately bound to a PV. Instead, the storage resources and PV will be generated and bound to the PVC only after the pod that requires the PVC is scheduled.                                                                                                                    |
   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | mountOptions                      | This field must be supported by the underlying storage. If this field is not supported but is specified, the PV creation will fail.                                                                                                                                                                                                                   |
   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_bestpractice_00281__cce_10_1068_table15415188175413:

.. table:: **Table 2** Parameters

   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Volume Type     | Parameter                          | Mandatory       | Description                                                                                                                                                                                         |
   +=================+====================================+=================+=====================================================================================================================================================================================================+
   | EVS             | csi.storage.k8s.io/csi-driver-name | Yes             | Driver type. If an EVS disk is used, the parameter value is fixed at **disk.csi.everest.io**.                                                                                                       |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | EVS             | csi.storage.k8s.io/fstype          | Yes             | If an EVS disk is used, the parameter value can be **ext4** or **xfs**.                                                                                                                             |
   |                 |                                    |                 |                                                                                                                                                                                                     |
   |                 |                                    |                 | The restrictions on using **xfs** are as follows:                                                                                                                                                   |
   |                 |                                    |                 |                                                                                                                                                                                                     |
   |                 |                                    |                 | -  The nodes must run CentOS 7, HCE OS 2.0, or Ubuntu 22.04, and the Everest version in the cluster must be 2.3.2 or later.                                                                         |
   |                 |                                    |                 | -  Only common containers are supported.                                                                                                                                                            |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | EVS             | everest.io/disk-volume-type        | Yes             | EVS disk type. All letters are in uppercase.                                                                                                                                                        |
   |                 |                                    |                 |                                                                                                                                                                                                     |
   |                 |                                    |                 | -  **SATA**: common I/O                                                                                                                                                                             |
   |                 |                                    |                 | -  **SAS**: high I/O                                                                                                                                                                                |
   |                 |                                    |                 | -  **SSD**: ultra-high I/O                                                                                                                                                                          |
   |                 |                                    |                 | -  **GPSSD**: general-purpose SSD                                                                                                                                                                   |
   |                 |                                    |                 | -  **ESSD**: extreme SSD                                                                                                                                                                            |
   |                 |                                    |                 | -  **GPSSD2**: general-purpose SSD v2, which is supported when the Everest version is 2.4.4 or later and the **everest.io/disk-iops** and **everest.io/disk-throughput** annotations are configured |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | EVS             | everest.io/passthrough             | Yes             | The parameter value is fixed at **true**, which indicates that the EVS device type is **SCSI**. No other parameter values are allowed.                                                              |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | SFS             | csi.storage.k8s.io/csi-driver-name | Yes             | Driver type. If SFS is used, the parameter value is fixed at **nas.csi.everest.io**.                                                                                                                |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | SFS             | csi.storage.k8s.io/fstype          | Yes             | If SFS is used, the value can be **nfs**.                                                                                                                                                           |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | SFS             | everest.io/share-access-level      | Yes             | The parameter value is fixed at **rw**, indicating that the SFS data is readable and writable.                                                                                                      |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | SFS             | everest.io/share-access-to         | Yes             | VPC ID of the cluster.                                                                                                                                                                              |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | SFS             | everest.io/share-is-public         | No              | The parameter value is fixed at **false**, indicating that the file is shared to private.                                                                                                           |
   |                 |                                    |                 |                                                                                                                                                                                                     |
   |                 |                                    |                 | When you use a general purpose file system (SFS 3.0 Capacity-Oriented), there is no need to configure this parameter.                                                                               |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | SFS             | everest.io/sfs-version             | No              | This parameter is only required for general purpose file systems (SFS 3.0 Capacity-Oriented) and its value is fixed at **sfs3.0**.                                                                  |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | SFS Turbo       | csi.storage.k8s.io/csi-driver-name | Yes             | Driver type. If SFS Turbo is used, the parameter value is fixed at **sfsturbo.csi.everest.io**.                                                                                                     |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | SFS Turbo       | csi.storage.k8s.io/fstype          | Yes             | If SFS Turbo is used, the value can be **nfs**.                                                                                                                                                     |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | SFS Turbo       | everest.io/share-access-to         | Yes             | VPC ID of the cluster.                                                                                                                                                                              |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | SFS Turbo       | everest.io/share-expand-type       | No              | Extension type. The default value is **bandwidth**, indicating an enhanced file system. This parameter does not take effect.                                                                        |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | SFS Turbo       | everest.io/share-source            | Yes             | The parameter value is fixed at **sfs-turbo**.                                                                                                                                                      |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | SFS Turbo       | everest.io/share-volume-type       | No              | SFS Turbo StorageClass. The default value is **STANDARD**, indicating standard and standard enhanced editions. This parameter does not take effect.                                                 |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | SFS Turbo       | everest.io/reclaim-policy          | No              | Whether to retain subdirectories when deleting a PVC. This parameter must be used with **reclaimPolicy**. This parameter is available only when the PV reclaim policy is **Delete**. Options:       |
   |                 |                                    |                 |                                                                                                                                                                                                     |
   |                 |                                    |                 | -  **retain-volume-only**: When a PVC is deleted, the PV is deleted, but **its associated subdirectories are retained**.                                                                            |
   |                 |                                    |                 | -  **delete**: When a PVC is deleted, **the PV and its associated subdirectories are also deleted**.                                                                                                |
   |                 |                                    |                 |                                                                                                                                                                                                     |
   |                 |                                    |                 |    .. note::                                                                                                                                                                                        |
   |                 |                                    |                 |                                                                                                                                                                                                     |
   |                 |                                    |                 |       When a subdirectory is deleted, only the absolute path of the subdirectory specified in the PVC is deleted. The parent directory remains unchanged.                                           |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | OBS             | csi.storage.k8s.io/csi-driver-name | Yes             | Driver type. If OBS is used, the parameter value is fixed at **obs.csi.everest.io**.                                                                                                                |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | OBS             | csi.storage.k8s.io/fstype          | Yes             | Instance type, which can be **obsfs** or **s3fs**.                                                                                                                                                  |
   |                 |                                    |                 |                                                                                                                                                                                                     |
   |                 |                                    |                 | -  **obsfs**: a parallel file system                                                                                                                                                                |
   |                 |                                    |                 | -  **s3fs**: object bucket                                                                                                                                                                          |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | OBS             | everest.io/obs-volume-type         | Yes             | OBS StorageClass.                                                                                                                                                                                   |
   |                 |                                    |                 |                                                                                                                                                                                                     |
   |                 |                                    |                 | -  If **fsType** is set to **s3fs**, standard buckets (**STANDARD**) and infrequent access buckets (**WARM**) are supported.                                                                        |
   |                 |                                    |                 | -  This parameter is invalid when **fsType** is set to **obsfs**.                                                                                                                                   |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Local PV        | csi.storage.k8s.io/csi-driver-name | Yes             | Driver type. If a local PV is used, the parameter value is fixed at **local.csi.everest.io**.                                                                                                       |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Local PV        | csi.storage.k8s.io/fstype          | Yes             | File system type. The value can only be **ext4**.                                                                                                                                                   |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Local PV        | volume-type                        | Yes             | Volume type. The value can only be **persistent**.                                                                                                                                                  |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

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

   The reclamation policy configured here has no impact on the SFS Turbo volumes.

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

Specifying a Default Storage Class
----------------------------------

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

When creating a PVC, you have the following configuration methods:

-  Specify a storage class explicitly.
-  Use the default storage class set up in the cluster if you do not specify a storage class.

Below are the steps to check the results of both configuration methods.

The following steps are used to verify whether the storage class **csi-disk-sas** defined in "Custom Storage Classes" can be used to create a PVC.

#. Create a YAML file named **sas-disk.yaml**. You can change the file name as needed. This file uses **csi-disk-sas** to create a PVC.

   .. code-block::

      vim sas-disk.yaml

   The file content is as follows:

   .. code-block::

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        name: sas-disk
      spec:
        accessModes:
        - ReadWriteOnce
        resources:
          requests:
            storage: 10Gi
        storageClassName: csi-disk-sas

#. Create a PVC.

   .. code-block::

      kubectl create -f sas-disk.yaml

   Information similar to the following is displayed:

   .. code-block::

      persistentvolumeclaim/sas-disk created

#. Check the PVC information.

   .. code-block::

      kubectl get pvc

   If information similar to the following is displayed, a PV has been associated with the PVC and the storage class is **csi-disk-sas**.

   .. code-block::

      NAME       STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
      sas-disk   Bound    pvc-6e2f37f9-7346-4419-82f7-b42e79f7964c   10Gi       RWO            csi-disk-sas   24s

#. Check the PV information.

   .. code-block::

      kubectl get pv

   If information similar to the following is displayed, the PV has been automatically created.

   .. code-block::

      NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM                     STORAGECLASS   REASON   AGE
      pvc-6e2f37f9-7346-4419-82f7-b42e79f7964c   10Gi       RWO            Delete           Bound       default/sas-disk          csi-disk-sas            30s

   The result shows that the custom storage class **csi-disk-sas** can be used properly.

The following steps are used to verify whether a PVC can be created without specifying a storage class.

#. Create a YAML file named **sas-disk.yaml**. You can change the file name as needed. When the file is used to create a PVC, no storage class is specified.

   .. code-block::

      vim sas-disk.yaml

   The file content is as follows:

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

#. Create a PVC.

   .. code-block::

      kubectl create -f sas-disk.yaml

   Information similar to the following is displayed:

   .. code-block::

      persistentvolumeclaim/sas-disk created

#. Check the PVC information.

   .. code-block::

      kubectl get pvc

   If information similar to the following is displayed, a PV has been bound to the PVC and the default storage class of the EVS disk is used.

   .. code-block::

      NAME       STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
      sas-disk   Bound    pvc-6e2f37f9-7346-4419-82f7-b42e79f7964c   10Gi       RWO            csi-disk-sas   16m
      ssd-disk   Bound    pvc-4d2b059c-0d6c-44af-9994-f74d01c78731   10Gi       RWO            csi-disk-ssd   10s

#. Check the PV information.

   .. code-block::

      kubectl get pv

   If information similar to the following is displayed, the PV has been automatically created and the default storage class of the EVS disk is used.

   .. code-block::

      NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM                     STORAGECLASS   REASON   AGE
      pvc-4d2b059c-0d6c-44af-9994-f74d01c78731   10Gi       RWO            Delete           Bound       default/ssd-disk          csi-disk-ssd            15s
      pvc-6e2f37f9-7346-4419-82f7-b42e79f7964c   10Gi       RWO            Delete           Bound       default/sas-disk          csi-disk-sas            17m

   The result shows that if no storage class is specified, the system will automatically use the default storage class.

.. |image1| image:: /_static/images/en-us_image_0000002516078763.png
