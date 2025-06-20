:original_name: cce_bestpractice_0024.html

.. _cce_bestpractice_0024:

Migrating Resources in a Cluster
================================

Application Scenarios
---------------------

WordPress is used as an example to describe how to migrate an application from an on-premises Kubernetes cluster to a CCE cluster. The WordPress application consists of the WordPress and MySQL components, which are containerized. The two components are bound to two local storage volumes of the local type, respectively, and provide external access through the NodePort Services.

Before the migration, you can use a browser to access the WordPress site, create a site named **Migrate to CCE**, and publish an article for verifying the integrity of PV data after the migration. The article published in WordPress will be stored in the **wp_posts** table of the MySQL database. If the migration is successful, all contents in the database will be migrated to the new cluster. You can then verify the migration results of the PV data.

Prerequisites
-------------

-  Before the migration, the abnormal pod resources in the source cluster have been cleared. If a pod with PVC mounted becomes abnormal, the PVC will be in the pending state after the migration.
-  The CCE cluster does not have the same resources as the source cluster because Velero does not restore the same resources by default.
-  The container images have been migrated to SWR so that they can be properly pulled after the migration.
-  CCE does not support EVS disks of the **ReadWriteMany** type. If resources of this type exist in the source cluster, change the storage type to **ReadWriteOnce**.
-  Velero **cannot back up or restore hostPath volumes**. For details, see `Limitations <https://velero.io/docs/v1.13/file-system-backup/#limitations>`__. To back up storage volumes of this type, replace the hostPath volumes with local volumes. If there is a hostPath volume in a backup task, the backup of this volume will be automatically skipped and a warning message will be generated. This will not cause a backup failure.

.. _cce_bestpractice_0024__section750718193288:

Backing Up an Application in the Source Cluster
-----------------------------------------------

#. .. _cce_bestpractice_0024__li686918502812:

   (Optional) To back up the data of a specified storage volume in a pod, add an annotation to the pod. The annotation template is as follows:

   .. code-block::

      kubectl -n <namespace> annotate <pod/pod_name> backup.velero.io/backup-volumes=<volume_name_1>,<volume_name_2>,...

   -  **<namespace>**: namespace where the pod is located
   -  **<pod_name>**: pod name
   -  **<volume_name>**: name of the PV mounted to the pod. You can run the **describe** statement to obtain the pod information. The **Volume** field indicates the names of all PVs mounted to the pod.

   You can add annotations to the WordPress pod **wordpress-758fbf6fc7-s7fsr** and MySQL pod **mysql-5ffdfbc498-c45lh**. These pods are in the default namespace **default**, so the **-n <NAMESPACE>** parameter can be omitted.

   .. code-block::

      kubectl annotate pod/wordpress-758fbf6fc7-s7fsr backup.velero.io/backup-volumes=wp-storage
      kubectl annotate pod/mysql-5ffdfbc498-c45lh backup.velero.io/backup-volumes=mysql-storage

#. Back up the application. During the backup, you can specify resources based on parameters. If no parameter is added, the entire cluster resources are backed up by default. For details about the parameters, see `Resource filtering <https://velero.io/docs/v1.13/resource-filtering/>`__.

   -  **--default-volumes-to-fs-backup**: indicates that the PV backup tool is used to back up all storage volumes attached to a pod. hostPath volumes are not supported. If this parameter is not specified, the storage volume specified by annotation in :ref:`1 <cce_bestpractice_0024__li686918502812>` is backed up by default. This parameter is available only when **--use-node-agent** is specified during :ref:`Velero installation <cce_bestpractice_0310__li1722825643415>`.

      .. code-block::

         velero backup create <backup-name> --default-volumes-to-fs-backup

   -  **--include-namespaces**: backs up resources in a specified namespace.

      .. code-block::

         velero backup create <backup-name> --include-namespaces <namespace>

   -  **--include-resources**: backs up the specified resources.

      .. code-block::

         velero backup create <backup-name> --include-resources deployments

   -  **--selector**: backs up resources that match the selector.

      .. code-block::

         velero backup create <backup-name> --selector <key>=<value>

   In this section, resources in the namespace **default** are backed up. **wordpress-backup** is the backup name. Specify the same backup name when restoring applications. An example is as follows:

   .. code-block::

      velero backup create wordpress-backup --include-namespaces default --default-volumes-to-fs-backup

   If information similar to the following is displayed, the backup task has been created:

   .. code-block::

      Backup request "wordpress-backup" submitted successfully.
      Run `velero backup describe wordpress-backup` or `velero backup logs wordpress-backup` for more details.

#. Check the backup status.

   .. code-block::

      velero backup get

   Information similar to the following is displayed:

   .. code-block::

      NAME               STATUS      ERRORS   WARNINGS   CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
      wordpress-backup   Completed   0        0          2021-10-14 15:32:07 +0800 CST   29d       default            <none>

   In addition, you can go to the object bucket to view the backup files. The **backups** path is for the application resource backup, and the other is for the PV data backup.

   |image1|

.. _cce_bestpractice_0024__section482103142819:

Restoring the Application in the Target Cluster
-----------------------------------------------

The storage infrastructure of an on-premises cluster is different from that of a cloud cluster. After the cluster is migrated, PVs cannot be mounted to pods. Therefore, during the migration, update the storage class of the target cluster to shield the differences of underlying storage interfaces between the two clusters when creating a workload and request storage resources of the corresponding type. For details, see :ref:`Updating the Storage Class <cce_bestpractice_0312__section746195321414>`.

#. Use kubectl to access the CCE cluster and create a storage class with the same name as that of the source cluster.

   In this example, the storage class name of the source cluster is **local** and the storage type is local disk. Local disks completely depend on the node availability. The data DR performance is poor. When the node is unavailable, the existing storage data is affected. Therefore, the storage resources in the CCE cluster should be EVS volumes, and the backend storage media should be SAS disks.

   .. note::

      -  When an application containing PV data is restored in a CCE cluster, the defined storage class dynamically creates storage resources (such as EVS volumes) based on the PVC and mounts the resources to the application.
      -  You can modify the storage resources of the cluster as needed, without being restricted to EVS volumes. To mount other types of storage, such as file storage and object storage, see :ref:`Updating the Storage Class <cce_bestpractice_0312__section746195321414>`.

   An example of the YAML file of the source cluster is as follows:

   .. code-block::

      apiVersion: storage.k8s.io/v1
      kind: StorageClass
      metadata:
        name: local
      provisioner: kubernetes.io/no-provisioner
      volumeBindingMode: WaitForFirstConsumer

   An example of the YAML file of the target cluster is as follows:

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

#. Create a Restore object using Velero, specify a backup named **wordpress-backup**, and restore the WordPress application in the CCE cluster.

   .. code-block::

      velero restore create --from-backup wordpress-backup

   You can run the **velero restore get** statement to check the application restoration.

#. After the restoration is complete, check whether the application is running properly. If other adaptation problems may occur, rectify the fault by following the procedure described in :ref:`Updating Resources Accordingly <cce_bestpractice_0312>`.

.. |image1| image:: /_static/images/en-us_image_0000002218818218.png
