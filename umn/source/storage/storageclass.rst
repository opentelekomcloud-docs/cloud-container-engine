:original_name: cce_10_0380.html

.. _cce_10_0380:

StorageClass
============

Introduction
------------

A StorageClass in Kubernetes is a resource that categorizes storage types in a cluster. It can also serve as a configuration template for creating PVs. When creating a PVC or PV, you must specify a StorageClass. This resource object defines the storage mode and configuration parameters for dynamic volume provisioning, such as volume type, access mode, and volume lifecycle policy.

When creating a PVC, you only need to specify the **StorageClassName**. Kubernetes will then automatically create the PVs and underlying storage based on the StorageClass. This greatly reduces the need for manual creation and maintenance of PVs.

In addition to the :ref:`default StorageClasses <cce_10_0380__section77737156273>` provided by CCE, you can customize StorageClasses. For details, see :ref:`Application Scenarios of Custom Storage <cce_10_0380__section18703932139>`.

.. _cce_10_0380__section11263115719212:

Creating a StorageClass Using the CCE Console
---------------------------------------------

.. note::

   The Everest add-on must be in the running state in the target cluster.

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Choose **Storage** in the navigation pane. In the right pane, click the **Storage Classes** tab. Click **Create Storage Class** in the upper right corner. In the dialog box displayed, configure parameters.

   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                           |
   +===================================+=======================================================================================================================================================================================================================================================================================================================================================+
   | StorageClass Type                 | Select an underlying storage type.                                                                                                                                                                                                                                                                                                                    |
   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Name                              | Enter the StorageClass name. The name of a StorageClass must be unique within a cluster.                                                                                                                                                                                                                                                              |
   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Reclaim Policy                    | You can select **Delete** or **Retain** to specify the reclaim policy of the underlying storage when the PVC is deleted. For details, see :ref:`PV Reclaim Policy <cce_10_0378__section19999142414413>`.                                                                                                                                              |
   |                                   |                                                                                                                                                                                                                                                                                                                                                       |
   |                                   | -  **Delete**: When a PVC is deleted, its associated underlying storage resources will be deleted and the PV resources will be removed. Exercise caution if you select this option.                                                                                                                                                                   |
   |                                   | -  **Retain**: When a PVC is deleted, both of the PV and its associated underlying storage resources will be retained and the PV is marked as released. If you manually delete the PV afterwards, the underlying storage resources will not be deleted. To bind the PV to a new PVC, you need to remove the original binding information from the PV. |
   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Binding Mode                      | Time when a PV is dynamically created, which can be created immediately or with a delay.                                                                                                                                                                                                                                                              |
   |                                   |                                                                                                                                                                                                                                                                                                                                                       |
   |                                   | -  **Immediate**: After a PVC is created, the storage resources and PV will be created and associated with the PVC without delay. Local PVs do not support **Immediate**.                                                                                                                                                                             |
   |                                   | -  **WaitForFirstConsumer**: After a PVC is created, it will not be immediately bound to a PV. Instead, the storage resources and PV will be generated and bound to the PVC only after the pod that requires the PVC is scheduled. OBS, SFS, and SFS Turbo do not support **WaitForFirstConsumer**.                                                   |
   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Create**. On the **Storage Classes** tab page, view the created StorageClass and its information.

.. _cce_10_0380__section77737156273:

Creating a StorageClass Using a YAML File
-----------------------------------------

As of now, CCE provides StorageClasses such as csi-disk, csi-nas, and csi-obs by default. When defining a PVC, you can use a **StorageClassName** to automatically create a PV of the corresponding type and automatically create underlying storage resources.

Run the following kubectl command to obtain the StorageClasses that CCE supports. Use the CSI add-on provided by CCE to create a StorageClass.

.. code-block::

   # kubectl get sc
   NAME                PROVISIONER                     AGE
   csi-disk            everest-csi-provisioner         17d          # EVS disk
   csi-disk-topology   everest-csi-provisioner         17d          # EVS disks created with delay
   csi-nas             everest-csi-provisioner         17d          # SFS 1.0
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
     csi.storage.k8s.io/fstype: ext4    # (Optional) Set the file system type to xfs or ext4. If it is left blank, ext4 will be used by default.
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
   | parameters                        | Specifies the storage parameters, which vary with storage types. For details, see :ref:`Table 2 <cce_10_0380__table15415188175413>`.                                                                                                                                                                                                                  |
   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | reclaimPolicy                     | Specifies the value of **persistentVolumeReclaimPolicy** for creating a PV. The value can be **Delete** or **Retain**. If **reclaimPolicy** is not specified when a StorageClass object is created, the value defaults to **Delete**.                                                                                                                 |
   |                                   |                                                                                                                                                                                                                                                                                                                                                       |
   |                                   | -  **Delete**: When a PVC is deleted, its associated underlying storage resources will be deleted and the PV resources will be removed. Exercise caution if you select this option.                                                                                                                                                                   |
   |                                   | -  **Retain**: When a PVC is deleted, both of the PV and its associated underlying storage resources will be retained and the PV is marked as released. If you manually delete the PV afterwards, the underlying storage resources will not be deleted. To bind the PV to a new PVC, you need to remove the original binding information from the PV. |
   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | allowVolumeExpansion              | Specifies whether the PV of this StorageClass supports dynamic capacity expansion. The default value is **false**. Dynamic capacity expansion is implemented by the underlying storage add-on. This is only a switch.                                                                                                                                 |
   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | volumeBindingMode                 | Specifies the volume binding mode, that is, the time when a PV is dynamically created. The value can be **Immediate** or **WaitForFirstConsumer**.                                                                                                                                                                                                    |
   |                                   |                                                                                                                                                                                                                                                                                                                                                       |
   |                                   | -  **Immediate**: After a PVC is created, the storage resources and PV will be created and associated with the PVC without delay.                                                                                                                                                                                                                     |
   |                                   | -  **WaitForFirstConsumer**: After a PVC is created, it will not be immediately bound to a PV. Instead, the storage resources and PV will be generated and bound to the PVC only after the pod that requires the PVC is scheduled.                                                                                                                    |
   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | mountOptions                      | This field must be supported by the underlying storage. If this field is not supported but is specified, the PV creation will fail.                                                                                                                                                                                                                   |
   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_10_0380__table15415188175413:

.. table:: **Table 2** Parameters

   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | Volume Type     | Parameter                          | Mandatory       | Description                                                                                                                                         |
   +=================+====================================+=================+=====================================================================================================================================================+
   | EVS             | csi.storage.k8s.io/csi-driver-name | Yes             | Driver type. If an EVS disk is used, the parameter value is fixed at **disk.csi.everest.io**.                                                       |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | csi.storage.k8s.io/fstype          | Yes             | If an EVS disk is used, the parameter value can be **ext4** or **xfs**.                                                                             |
   |                 |                                    |                 |                                                                                                                                                     |
   |                 |                                    |                 | The restrictions on using **xfs** are as follows:                                                                                                   |
   |                 |                                    |                 |                                                                                                                                                     |
   |                 |                                    |                 | -  The nodes must run CentOS 7, HCE OS 2.0, or Ubuntu 22.04, and the Everest version in the cluster must be 2.3.2 or later.                         |
   |                 |                                    |                 | -  Only common containers are supported.                                                                                                            |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | everest.io/disk-volume-type        | Yes             | EVS disk type. All letters are in uppercase.                                                                                                        |
   |                 |                                    |                 |                                                                                                                                                     |
   |                 |                                    |                 | -  **SATA**: common I/O                                                                                                                             |
   |                 |                                    |                 | -  **SAS**: high I/O                                                                                                                                |
   |                 |                                    |                 | -  **SSD**: ultra-high I/O                                                                                                                          |
   |                 |                                    |                 | -  **GPSSD**: general-purpose SSD                                                                                                                   |
   |                 |                                    |                 | -  **ESSD**: extreme SSD                                                                                                                            |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | everest.io/passthrough             | Yes             | The parameter value is fixed at **true**, which indicates that the EVS device type is **SCSI**. Other parameter values are not allowed.             |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | SFS             | csi.storage.k8s.io/csi-driver-name | Yes             | Driver type. If SFS is used, the parameter value is fixed at **nas.csi.everest.io**.                                                                |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | csi.storage.k8s.io/fstype          | Yes             | If SFS is used, the value can be **nfs**.                                                                                                           |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | everest.io/share-access-level      | Yes             | The parameter value is fixed at **rw**, indicating that the SFS data is readable and writable.                                                      |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | everest.io/share-access-to         | Yes             | VPC ID of the cluster.                                                                                                                              |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | everest.io/share-is-public         | No              | The parameter value is fixed at **false**, indicating that the file is shared to private.                                                           |
   |                 |                                    |                 |                                                                                                                                                     |
   |                 |                                    |                 | When you use SFS 3.0, there is no need to configure this parameter.                                                                                 |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | everest.io/sfs-version             | No              | This parameter is only required for SFS 3.0 and its value is fixed at **sfs3.0**.                                                                   |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | SFS Turbo       | csi.storage.k8s.io/csi-driver-name | Yes             | Driver type. If SFS Turbo is used, the parameter value is fixed at **sfsturbo.csi.everest.io**.                                                     |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | csi.storage.k8s.io/fstype          | Yes             | If SFS Turbo is used, the value can be **nfs**.                                                                                                     |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | everest.io/share-access-to         | Yes             | VPC ID of the cluster.                                                                                                                              |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | everest.io/share-expand-type       | No              | Extension type. The default value is **bandwidth**, indicating an enhanced file system. This parameter does not take effect.                        |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | everest.io/share-source            | Yes             | The parameter value is fixed at **sfs-turbo**.                                                                                                      |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | everest.io/share-volume-type       | No              | SFS Turbo StorageClass. The default value is **STANDARD**, indicating standard and standard enhanced editions. This parameter does not take effect. |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   | OBS             | csi.storage.k8s.io/csi-driver-name | Yes             | Driver type. If OBS is used, the parameter value is fixed at **obs.csi.everest.io**.                                                                |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | csi.storage.k8s.io/fstype          | Yes             | Instance type, which can be **obsfs** or **s3fs**.                                                                                                  |
   |                 |                                    |                 |                                                                                                                                                     |
   |                 |                                    |                 | -  **obsfs**: a parallel file system                                                                                                                |
   |                 |                                    |                 | -  **s3fs**: object bucket                                                                                                                          |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+
   |                 | everest.io/obs-volume-type         | Yes             | OBS StorageClass.                                                                                                                                   |
   |                 |                                    |                 |                                                                                                                                                     |
   |                 |                                    |                 | -  If **fsType** is set to **s3fs**, **STANDARD** (standard bucket) and **WARM** (infrequent access bucket) are supported.                          |
   |                 |                                    |                 | -  This parameter is invalid when **fsType** is set to **obsfs**.                                                                                   |
   +-----------------+------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_10_0380__section18703932139:

Application Scenarios of Custom Storage
---------------------------------------

When using storage resources in CCE, the most common method is to specify **StorageClassName** to define the type of storage resources to be created when creating a PVC. The following configuration shows how to use a PVC to apply for a SAS (high I/O) EVS disk (block storage).

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

+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+
| Application Scenario                                                                                                                                                                                                                                                                                                                                       | Solution                                                                                                                                                                                                                                                                                 | Procedure                                                                                       |
+============================================================================================================================================================================================================================================================================================================================================================+==========================================================================================================================================================================================================================================================================================+=================================================================================================+
| When **annotations** is used to specify storage configuration, the configuration is complex. For example, the **everest.io/disk-volume-type** field is used to specify the EVS disk type.                                                                                                                                                                  | Define PVC annotations in the **parameters** field of StorageClass. When compiling a YAML file, you only need to specify **StorageClassName**.                                                                                                                                           | :ref:`Scenario 1: Specifying the Disk Type in a StorageClass <cce_10_0380__section92221021258>` |
|                                                                                                                                                                                                                                                                                                                                                            |                                                                                                                                                                                                                                                                                          |                                                                                                 |
|                                                                                                                                                                                                                                                                                                                                                            | For example, you can define SAS EVS disk and SSD EVS disk as a StorageClass, respectively. If a StorageClass named **csi-disk-sas** is defined, it is used to create SAS storage.                                                                                                        |                                                                                                 |
+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+
| When a user migrates services from an on-premises Kubernetes cluster or other Kubernetes services to CCE, the StorageClass used in the original application YAML file is different from that used in CCE. As a result, a large number of YAML files or Helm chart packages need to be modified when the storage is used, which is complex and error-prone. | Create a StorageClass with the same name as that in the original application YAML file in the CCE cluster. After the migration, you do not need to modify the **StorageClassName** in the application YAML file.                                                                         |                                                                                                 |
|                                                                                                                                                                                                                                                                                                                                                            |                                                                                                                                                                                                                                                                                          |                                                                                                 |
|                                                                                                                                                                                                                                                                                                                                                            | For example, the EVS disk StorageClass used before the migration is **disk-standard**. After migrating services to a CCE cluster, you can copy the YAML file of the **csi-disk** StorageClass in the CCE cluster, change its name to **disk-standard**, and create another StorageClass. |                                                                                                 |
+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+
| **StorageClassName** must be specified in the YAML file to use the storage. If not, the storage cannot be created.                                                                                                                                                                                                                                         | If you set the default StorageClass in the cluster, you can create storage without specifying the **StorageClassName** in the YAML file.                                                                                                                                                 | :ref:`Scenario 2: Specifying the Default StorageClass <cce_10_0380__section9720192252>`         |
+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------+

.. _cce_10_0380__section92221021258:

Scenario 1: Specifying the Disk Type in a StorageClass
------------------------------------------------------

This section uses the custom StorageClass of EVS disks as an example to describe how to define SAS EVS disk and SSD EVS disk as a StorageClass, respectively. For example, if you define a StorageClass named **csi-disk-sas**, which is used to create SAS storage, the differences are shown in the following figure. When editing a PVC's YAML file, you only need to specify **StorageClassName**.

|image1|

-  You can customize a high I/O StorageClass in a YAML file. For example, the name **csi-disk-sas** indicates that the disk type is SAS (high I/O).

   .. code-block::

      apiVersion: storage.k8s.io/v1
      kind: StorageClass
      metadata:
        name: csi-disk-sas                          # Name of the high I/O StorageClass, which can be customized
      parameters:
        csi.storage.k8s.io/csi-driver-name: disk.csi.everest.io
        csi.storage.k8s.io/fstype: ext4    # (Optional) Set the file system type to xfs or ext4. If it is left blank, ext4 will be used by default.
         everest.io/disk-volume-type: SAS            # High I/O EVS disk type, which cannot be customized.
        everest.io/passthrough: "true"
      provisioner: everest-csi-provisioner
      reclaimPolicy: Delete
      volumeBindingMode: Immediate
      allowVolumeExpansion: true                    # true indicates that capacity expansion is allowed.

-  For an ultra-high I/O StorageClass, you can set the class name to **csi-disk-ssd** to create SSD EVS disk (ultra-high I/O).

   .. code-block::

      apiVersion: storage.k8s.io/v1
      kind: StorageClass
      metadata:
        name: csi-disk-ssd                       # Name of the ultra-high I/O StorageClass, which can be customized
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

Scenario 2: Specifying the Default StorageClass
-----------------------------------------------

You can specify a StorageClass as the default class. In this way, any PVCs created without specifying **StorageClassName** will automatically use the default StorageClass.

For example, to specify **csi-disk-ssd** as the default StorageClass, edit your YAML file as follows:

.. code-block::

   apiVersion: storage.k8s.io/v1
   kind: StorageClass
   metadata:
     name: csi-disk-ssd
     annotations:
       storageclass.kubernetes.io/is-default-class: "true"   # Specify the default StorageClass in a cluster. There can only be one default StorageClass per cluster.
   parameters:
     csi.storage.k8s.io/csi-driver-name: disk.csi.everest.io
     csi.storage.k8s.io/fstype: ext4
     everest.io/disk-volume-type: SSD
     everest.io/passthrough: "true"
   provisioner: everest-csi-provisioner
   reclaimPolicy: Delete
   volumeBindingMode: Immediate
   allowVolumeExpansion: true

Run the **kubectl create** command to create a StorageClass and then check it.

.. code-block::

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

.. |image1| image:: /_static/images/en-us_image_0000002101678997.png
