:original_name: cce_bestpractice_0311.html

.. _cce_bestpractice_0311:

Migrating Resources in a Cluster
================================

Scenario
--------

WordPress is used as an example to describe how to migrate an application from an on-premises Kubernetes cluster to a CCE cluster. The WordPress application consists of the WordPress and MySQL components, which are containerized. The two components are bound to two local storage volumes of the Local type respectively and provide external access through the NodePort Service.

Before the migration, use a browser to access the WordPress site, create a site named **Migrate to CCE**, and publish an article to verify the integrity of PV data after the migration. The article published in WordPress will be stored in the **wp_posts** table of the MySQL database. If the migration is successful, all contents in the database will be migrated to the new cluster. You can verify the PV data migration based on the migration result.

Prerequisites
-------------

-  Before the migration, clear the abnormal pod resources in the source cluster. If the pod is in the abnormal state and has a PVC mounted, the PVC is in the pending state after the cluster is migrated.
-  Ensure that the cluster on the CCE side does not have the same resources as the cluster to be migrated because Velero does not restore the same resources by default.
-  To ensure that container image images can be properly pulled after cluster migration, migrate the images to SWR.
-  CCE does not support EVS disks of the **ReadWriteMany** type. If resources of this type exist in the source cluster, change the storage type to **ReadWriteOnce**.
-  Velero integrates the Restic tool to back up and restore storage volumes. Currently, the storage volumes of the HostPath type are not supported. For details, see `Restic Restrictions <https://velero.io/docs/v1.7/restic/#limitations>`__. If you need to back up storage volumes of this type, replace the hostPath volumes with local volumes by referring to :ref:`Storage Volumes of the HostPath Type Cannot Be Backed Up <cce_bestpractice_0314__section11197194820367>`. If a backup task involves storage of the HostPath type, the storage volumes of this type will be automatically skipped and a warning message will be generated. This will not cause a backup failure.

.. _cce_bestpractice_0311__section750718193288:

Backing Up Applications in the Source Cluster
---------------------------------------------

#. .. _cce_bestpractice_0311__li686918502812:

   (Optional) If you need to back up the data of a specified storage volume in the pod, add an annotation to the pod. The annotation template is as follows:

   .. code-block::

      kubectl -n <namespace> annotate <pod/pod_name> backup.velero.io/backup-volumes=<volume_name_1>,<volume_name_2>,...

   -  **<namespace>**: namespace where the pod is located.
   -  **<pod_name>**: pod name.
   -  **<volume_name>**: name of the persistent volume mounted to the pod. You can run the **describe** statement to query the pod information. The **Volume** field indicates the names of all persistent volumes attached to the pod.

   Add annotations to the pods of WordPress and MySQL. The pod names are **wordpress-758fbf6fc7-s7fsr** and **mysql-5ffdfbc498-c45lh**. As the pods are in the default namespace **default**, the **-n <NAMESPACE>** parameter can be omitted.

   .. code-block::

      kubectl annotate pod/wordpress-758fbf6fc7-s7fsr backup.velero.io/backup-volumes=wp-storage
      kubectl annotate pod/mysql-5ffdfbc498-c45lh backup.velero.io/backup-volumes=mysql-storage

#. Back up the application. During the backup, you can specify resources based on parameters. If no parameter is added, the entire cluster resources are backed up by default. For details about the parameters, see `Resource filtering <https://velero.io/docs/v1.7/resource-filtering/>`__.

   -  **--default-volumes-to-restic**: indicates that the Restic tool is used to back up all storage volumes mounted to the pod. Storage volumes of the HostPath type are not supported. If this parameter is not specified, the storage volume specified by annotation in :ref:`1 <cce_bestpractice_0311__li686918502812>` is backed up by default. This parameter is available only when **--use-restic** is specified during :ref:`Velero installation <cce_bestpractice_0310__li1722825643415>`.

      .. code-block::

         velero backup create <backup-name> --default-volumes-to-restic

   -  **--include-namespaces**: backs up resources in a specified namespace.

      .. code-block::

         velero backup create <backup-name> --include-namespaces <namespace>

   -  **--include-resources**: backs up the specified resources.

      .. code-block::

         velero backup create <backup-name> --include-resources deployments

   -  **--selector**: backs up resources that match the selector.

      .. code-block::

         velero backup create <backup-name> --selector <key>=<value>

   In this section, resources in the namespace **default** are backed up. **wordpress-backup** is the backup name. You need to specify the same backup name when restoring applications. Example:

   .. code-block::

      velero backup create wordpress-backup --include-namespaces default --default-volumes-to-restic

   If the following information is displayed, the backup task is successfully created:

   .. code-block::

      Backup request "wordpress-backup" submitted successfully. Run `velero backup describe wordpress-backup` or `velero backup logs wordpress-backup` for more details.

#. Check the backup status.

   .. code-block::

      velero backup get

   Information similar to the following is displayed:

   .. code-block::

      NAME               STATUS      ERRORS   WARNINGS   CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
      wordpress-backup   Completed   0        0          2021-10-14 15:32:07 +0800 CST   29d       default            <none>

   In addition, you can go to the object bucket to view the backup files. The backups path is the application resource backup path, and the restic path is the PV data backup path.

   |image1|

.. _cce_bestpractice_0311__section482103142819:

Restoring Applications in the Target Cluster
--------------------------------------------

The storage infrastructure of an on-premises cluster is different from that of a cloud cluster. After the cluster is migrated, PVs cannot be mounted to pods. Therefore, during the migration, you need to update the storage class of the target cluster to shield the differences of underlying storage interfaces between the two clusters when creating a workload and request storage resources of the corresponding type. For details, see :ref:`Updating the Storage Class <cce_bestpractice_0312__section746195321414>`.

#. Use kubectl to connect to the CCE cluster. Create a storage class with the same name as that of the source cluster.

   In this example, the storage class name of the source cluster is **local** and the storage type is local disk. Local disks completely depend on the node availability. The data DR performance is poor. When the node is unavailable, the existing storage data is affected. Therefore, EVS volumes are used as storage resources in CCE clusters, and SAS disks are used as backend storage media.

   .. note::

      -  When an application containing PV data is restored in a CCE cluster, the defined storage class dynamically creates and mounts storage resources (such as EVS volumes) based on the PVC.
      -  The storage resources of the cluster can be changed as required, not limited to EVS volumes. To mount other types of storage, such as file storage and object storage, see :ref:`Updating the Storage Class <cce_bestpractice_0312__section746195321414>`.

   YAML file of the migrated cluster:

   .. code-block::

      apiVersion: storage.k8s.io/v1
      kind: StorageClass
      metadata:
        name: local
      provisioner: kubernetes.io/no-provisioner
      volumeBindingMode: WaitForFirstConsumer

   The following is an example of the YAML file of the migration cluster:

   .. code-block::

      allowVolumeExpansion: true
      apiVersion: storage.k8s.io/v1
      kind: StorageClass
      metadata:
        name: local
        selfLink: /apis/storage.k8s.io/v1/storageclasses/csi-disk
      parameters:
        csi.storage.k8s.io/csi-driver-name: disk.csi.everest.io
        csi.storage.k8s.io/fstype: ext4
        everest.io/disk-volume-type: SAS
        everest.io/passthrough: "true"
      provisioner: everest-csi-provisioner
      reclaimPolicy: Delete
      volumeBindingMode: Immediate

#. Use the Velero tool to create a restore and specify a backup named **wordpress-backup** to restore the WordPress application to the CCE cluster.

   .. code-block::

      velero restore create --from-backup wordpress-backup

   You can run the **velero restore get** statement to view the application restoration status.

#. After the restoration is complete, check whether the application is running properly. If other adaptation problems may occur, rectify the fault by following the procedure described in :ref:`Updating Resources Accordingly <cce_bestpractice_0312>`.

.. |image1| image:: /_static/images/en-us_image_0000001480191270.png
