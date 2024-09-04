:original_name: cce_10_0380.html

.. _cce_10_0380:

StorageClass
============

Introduction
------------

StorageClass describes the classification of storage types in a cluster and can be represented as a configuration template for creating PVs. When creating a PVC or PV, specify StorageClass.

As a user, you only need to specify **storageClassName** when defining a PVC to automatically create a PV and underlying storage, significantly reducing the workload of creating and maintaining a PV.

In addition to the :ref:`default storage classes <cce_10_0380__section77737156273>` provided by CCE, you can also customize storage classes.

-  :ref:`Application Scenarios of Custom Storage <cce_10_0380__section18703932139>`
-  :ref:`Custom Storage Class <cce_10_0380__section92221021258>`
-  :ref:`Specifying a Default Storage Class <cce_10_0380__section9720192252>`

.. _cce_10_0380__section77737156273:

CCE Default Storage Classes
---------------------------

As of now, CCE provides storage classes such as csi-disk, csi-nas, and csi-obs by default. When defining a PVC, you can use a **storageClassName** to automatically create a PV of the corresponding type and automatically create underlying storage resources.

Run the following kubectl command to obtain the storage classes that CCE supports. Use the CSI add-on provided by CCE to create a storage class.

.. code-block::

   # kubectl get sc
   NAME                PROVISIONER                     AGE
   csi-disk            everest-csi-provisioner         17d          # EVS disk
   csi-disk-topology   everest-csi-provisioner         17d          # EVS disks created with delay
   csi-nas             everest-csi-provisioner         17d          # SFS 1.0
   csi-obs             everest-csi-provisioner         17d          # OBS
   csi-sfsturbo        everest-csi-provisioner         17d          # SFS Turbo

Each storage class contains the default parameters used for dynamically creating a PV. The following is an example of storage class for EVS disks:

.. code-block::

   kind: StorageClass
   apiVersion: storage.k8s.io/v1
   metadata:
     name: csi-disk
   provisioner: everest-csi-provisioner
   parameters:
     csi.storage.k8s.io/csi-driver-name: disk.csi.everest.io
   csi.storage.k8s.io/fstype: ext4    # (Optional) Set the file system type to xfs or ext4. If it is left blank, ext4 will be used by default.
     everest.io/disk-volume-type: SAS
     everest.io/passthrough: 'true'
   reclaimPolicy: Delete
   allowVolumeExpansion: true
   volumeBindingMode: Immediate

.. table:: **Table 1** Key parameters

   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                         | Description                                                                                                                                                                                                                           |
   +===================================+=======================================================================================================================================================================================================================================+
   | provisioner                       | Specifies the storage resource provider, which is the Everest add-on for CCE. Set this parameter to **everest-csi-provisioner**.                                                                                                      |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | parameters                        | Specifies the storage parameters, which vary with storage types. For details, see :ref:`Table 2 <cce_10_0380__table15415188175413>`.                                                                                                  |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | reclaimPolicy                     | Specifies the value of **persistentVolumeReclaimPolicy** for creating a PV. The value can be **Delete** or **Retain**. If **reclaimPolicy** is not specified when a StorageClass object is created, the value defaults to **Delete**. |
   |                                   |                                                                                                                                                                                                                                       |
   |                                   | -  **Delete**: indicates that a dynamically created PV will be automatically deleted when the PVC is deleted.                                                                                                                         |
   |                                   | -  **Retain**: indicates that a dynamically created PV will be retained when the PVC is deleted.                                                                                                                                      |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | allowVolumeExpansion              | Specifies whether the PV of this storage class supports dynamic capacity expansion. The default value is **false**. Dynamic capacity expansion is implemented by the underlying storage add-on. This is only a switch.                |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | volumeBindingMode                 | Specifies the volume binding mode, that is, the time when a PV is dynamically created. The value can be **Immediate** or **WaitForFirstConsumer**.                                                                                    |
   |                                   |                                                                                                                                                                                                                                       |
   |                                   | -  **Immediate**: PV binding and dynamic creation are completed when a PVC is created.                                                                                                                                                |
   |                                   | -  **WaitForFirstConsumer**: PV binding and creation are delayed. The PV creation and binding processes are executed only when the PVC is used in the workload.                                                                       |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | mountOptions                      | This field must be supported by the underlying storage. If this field is not supported but is specified, the PV creation will fail.                                                                                                   |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_10_0380__table15415188175413:

.. table:: **Table 2** Parameters

   +-----------------+------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Volume Type     | Parameter                          | Mandatory       | Description                                                                                                                                          |
   +=================+====================================+=================+======================================================================================================================================================+
   | EVS             | csi.storage.k8s.io/csi-driver-name | Yes             | Driver type. If an EVS disk is used, the parameter value is fixed at **disk.csi.everest.io**.                                                        |
   +-----------------+------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | csi.storage.k8s.io/fstype          | Yes             | If an EVS disk is used, the parameter value can be **ext4** or **xfs**.                                                                              |
   |                 |                                    |                 |                                                                                                                                                      |
   |                 |                                    |                 | The restrictions on using **xfs** are as follows:                                                                                                    |
   |                 |                                    |                 |                                                                                                                                                      |
   |                 |                                    |                 | -  The nodes must run CentOS 7 or Ubuntu 22.04, and the Everest version in the cluster must be 2.3.2 or later.                                       |
   |                 |                                    |                 | -  Only common containers are supported.                                                                                                             |
   +-----------------+------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | everest.io/disk-volume-type        | Yes             | EVS disk type. All letters are in uppercase.                                                                                                         |
   |                 |                                    |                 |                                                                                                                                                      |
   |                 |                                    |                 | -  **SATA**: common I/O                                                                                                                              |
   |                 |                                    |                 | -  **SAS**: high I/O                                                                                                                                 |
   |                 |                                    |                 | -  **SSD**: ultra-high I/O                                                                                                                           |
   +-----------------+------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | everest.io/passthrough             | Yes             | The parameter value is fixed at **true**, which indicates that the EVS device type is **SCSI**. Other parameter values are not allowed.              |
   +-----------------+------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------+
   | SFS             | csi.storage.k8s.io/csi-driver-name | Yes             | Driver type. If SFS is used, the parameter value is fixed at **nas.csi.everest.io**.                                                                 |
   +-----------------+------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | csi.storage.k8s.io/fstype          | Yes             | If SFS is used, the value can be **nfs**.                                                                                                            |
   +-----------------+------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | everest.io/share-access-level      | Yes             | The parameter value is fixed at **rw**, indicating that the SFS data is readable and writable.                                                       |
   +-----------------+------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | everest.io/share-access-to         | Yes             | VPC ID of the cluster.                                                                                                                               |
   +-----------------+------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | everest.io/share-is-public         | No              | The parameter value is fixed at **false**, indicating that the file is shared to private.                                                            |
   |                 |                                    |                 |                                                                                                                                                      |
   |                 |                                    |                 | You do not need to configure this parameter when SFS 3.0 is used.                                                                                    |
   +-----------------+------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | everest.io/sfs-version             | No              | This parameter is mandatory only when SFS 3.0 is used. The value is fixed at **sfs3.0**.                                                             |
   +-----------------+------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------+
   | SFS Turbo       | csi.storage.k8s.io/csi-driver-name | Yes             | Driver type. If SFS Turbo is used, the parameter value is fixed at **sfsturbo.csi.everest.io**.                                                      |
   +-----------------+------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | csi.storage.k8s.io/fstype          | Yes             | If SFS Turbo is used, the value can be **nfs**.                                                                                                      |
   +-----------------+------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | everest.io/share-access-to         | Yes             | VPC ID of the cluster.                                                                                                                               |
   +-----------------+------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | everest.io/share-expand-type       | No              | Extension type. The default value is **bandwidth**, indicating an enhanced file system. This parameter does not take effect.                         |
   +-----------------+------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | everest.io/share-source            | Yes             | The parameter value is fixed at **sfs-turbo**.                                                                                                       |
   +-----------------+------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | everest.io/share-volume-type       | No              | SFS Turbo storage class. The default value is **STANDARD**, indicating standard and standard enhanced editions. This parameter does not take effect. |
   +-----------------+------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------+
   | OBS             | csi.storage.k8s.io/csi-driver-name | Yes             | Driver type. If OBS is used, the parameter value is fixed at **obs.csi.everest.io**.                                                                 |
   +-----------------+------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | csi.storage.k8s.io/fstype          | Yes             | Instance type, which can be **obsfs** or **s3fs**.                                                                                                   |
   |                 |                                    |                 |                                                                                                                                                      |
   |                 |                                    |                 | -  **obsfs**: a parallel file system                                                                                                                 |
   |                 |                                    |                 | -  **s3fs**: object bucket                                                                                                                           |
   +-----------------+------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | everest.io/obs-volume-type         | Yes             | OBS storage class.                                                                                                                                   |
   |                 |                                    |                 |                                                                                                                                                      |
   |                 |                                    |                 | -  If **fsType** is set to **s3fs**, **STANDARD** (standard bucket) and **WARM** (infrequent access bucket) are supported.                           |
   |                 |                                    |                 | -  This parameter is invalid when **fsType** is set to **obsfs**.                                                                                    |
   +-----------------+------------------------------------+-----------------+------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_10_0380__section18703932139:

Application Scenarios of Custom Storage
---------------------------------------

When using storage resources in CCE, the most common method is to specify **storageClassName** to define the type of storage resources to be created when creating a PVC. The following configuration shows how to use a PVC to apply for a SAS (high I/O) EVS disk (block storage).

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

To specify the EVS disk type on CCE, use the **everest.io/disk-volume-type** field. SAS indicates the EVS disk type.

The preceding is a basic method of using StorageClass. In real-world scenarios, you can use StorageClass to perform other operations.

+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------+
| Application Scenario                                                                                                                                                                                                                                                                                                                                      | Solution                                                                                                                                                                                                                                                                                    | Procedure                                                                  |
+===========================================================================================================================================================================================================================================================================================================================================================+=============================================================================================================================================================================================================================================================================================+============================================================================+
| When **annotations** is used to specify storage configuration, the configuration is complex. For example, the **everest.io/disk-volume-type** field is used to specify the EVS disk type.                                                                                                                                                                 | Define PVC annotations in the **parameters** field of StorageClass. When compiling a YAML file, you only need to specify **storageClassName**.                                                                                                                                              | :ref:`Custom Storage Class <cce_10_0380__section92221021258>`              |
|                                                                                                                                                                                                                                                                                                                                                           |                                                                                                                                                                                                                                                                                             |                                                                            |
|                                                                                                                                                                                                                                                                                                                                                           | For example, you can define SAS EVS disk and SSD EVS disk as a storage class, respectively. If a storage class named **csi-disk-sas** is defined, it is used to create SAS storage.                                                                                                         |                                                                            |
+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------+
| When a user migrates services from a self-built Kubernetes cluster or other Kubernetes services to CCE, the storage class used in the original application YAML file is different from that used in CCE. As a result, a large number of YAML files or Helm chart packages need to be modified when the storage is used, which is complex and error-prone. | Create a storage class with the same name as that in the original application YAML file in the CCE cluster. After the migration, you do not need to modify the **storageClassName** in the application YAML file.                                                                           |                                                                            |
|                                                                                                                                                                                                                                                                                                                                                           |                                                                                                                                                                                                                                                                                             |                                                                            |
|                                                                                                                                                                                                                                                                                                                                                           | For example, the EVS disk storage class used before the migration is **disk-standard**. After migrating services to a CCE cluster, you can copy the YAML file of the **csi-disk** storage class in the CCE cluster, change its name to **disk-standard**, and create another storage class. |                                                                            |
+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------+
| **storageClassName** must be specified in the YAML file to use the storage. If not, the storage cannot be created.                                                                                                                                                                                                                                        | If you set the default StorageClass in the cluster, you can create storage without specifying the **storageClassName** in the YAML file.                                                                                                                                                    | :ref:`Specifying a Default Storage Class <cce_10_0380__section9720192252>` |
+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------+

.. _cce_10_0380__section92221021258:

Custom Storage Class
--------------------

This section uses the custom storage class of EVS disks as an example to describe how to define SAS EVS disk and SSD EVS disk as a storage class, respectively. For example, if you define a storage class named **csi-disk-sas**, which is used to create SAS storage, the differences are shown in the following figure. When compiling a YAML file, you only need to specify **storageClassName**.

|image1|

-  You can customize a high I/O storage class in a YAML file. For example, the name **csi-disk-sas** indicates that the disk type is SAS (high I/O).

   .. code-block::

      apiVersion: storage.k8s.io/v1
      kind: StorageClass
      metadata:
        name: csi-disk-sas                          # Name of the high I/O storage class, which can be customized.
      parameters:
        csi.storage.k8s.io/csi-driver-name: disk.csi.everest.io
      csi.storage.k8s.io/fstype: ext4    # (Optional) Set the file system type to xfs or ext4. If it is left blank, ext4 will be used by default.
         everest.io/disk-volume-type: SAS            # High I/O EVS disk type, which cannot be customized.
        everest.io/passthrough: "true"
      provisioner: everest-csi-provisioner
      reclaimPolicy: Delete
      volumeBindingMode: Immediate
      allowVolumeExpansion: true                    # true indicates that capacity expansion is allowed.

-  For an ultra-high I/O storage class, you can set the class name to **csi-disk-ssd** to create SSD EVS disk (ultra-high I/O).

   .. code-block::

      apiVersion: storage.k8s.io/v1
      kind: StorageClass
      metadata:
        name: csi-disk-ssd                       # Name of the ultra-high I/O storage class, which can be customized.
      parameters:
        csi.storage.k8s.io/csi-driver-name: disk.csi.everest.io
      csi.storage.k8s.io/fstype: ext4    # (Optional) Set the file system type to xfs or ext4. If it is left blank, ext4 will be used by default.
        everest.io/disk-volume-type: SSD         # Ultra-high I/O EVS disk type, which cannot be customized.
        everest.io/passthrough: "true"
      provisioner: everest-csi-provisioner
      reclaimPolicy: Delete
      volumeBindingMode: Immediate
      allowVolumeExpansion: true

**reclaimPolicy**: indicates the reclaim policies of the underlying cloud storage. The value can be **Delete** or **Retain**.

-  **Delete**: When a PVC is deleted, both the PV and the EVS disk are deleted.
-  **Retain**: When a PVC is deleted, both the PV and underlying storage resources will be retained. You need to manually delete these resources. After the PVC is deleted, the PV is in the **Released** state and cannot be bound to a PVC again.

If high data security is required, select **Retain** to prevent data from being deleted by mistake.

After the definition is complete, run the **kubectl create** commands to create storage resources.

.. code-block::

   # kubectl create -f sas.yaml
   storageclass.storage.k8s.io/csi-disk-sas created
   # kubectl create -f ssd.yaml
   storageclass.storage.k8s.io/csi-disk-ssd created

Query **StorageClass** again. The command output is as follows:

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

.. _cce_10_0380__section9720192252:

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

.. |image1| image:: /_static/images/en-us_image_0000001981436605.png
