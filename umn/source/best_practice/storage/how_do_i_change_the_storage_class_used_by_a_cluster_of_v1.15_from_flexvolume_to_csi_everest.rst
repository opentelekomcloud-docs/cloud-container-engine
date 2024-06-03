:original_name: cce_bestpractice_0107.html

.. _cce_bestpractice_0107:

How Do I Change the Storage Class Used by a Cluster of v1.15 from FlexVolume to CSI Everest?
============================================================================================

In clusters later than v1.15.11-r1, CSI (the everest add-on) has taken over all functions of fuxi FlexVolume (the storage-driver add-on) for managing container storage. You are advised to use CSI Everest.

To migrate your storage volumes, create a static PV to associate with the original underlying storage, and then create a PVC to associate with this static PV. When you upgrade your application, mount the new PVC to the original mounting path to migrate the storage volumes.

.. warning::

   Services will be interrupted during the migration. Therefore, properly plan the migration and back up data.

Procedure
---------

#. (Optional) Back up data to prevent data loss in case of exceptions.

#. .. _cce_bestpractice_0107__li1219802032512:

   Configure a YAML file of the PV in the CSI format according to the PV in the FlexVolume format and associate the PV with the existing storage.

   To be specific, run the following commands to configure the pv-example.yaml file, which is used to create a PV.

   **touch pv-example.yaml**

   **vi pv-example.yaml**

   Configuration example of **a PV for an EVS volume**:

   .. code-block::

      apiVersion: v1
      kind: PersistentVolume
      metadata:
        labels:
          failure-domain.beta.kubernetes.io/region: eu-de
          failure-domain.beta.kubernetes.io/zone: <zone name>
        annotations:
          pv.kubernetes.io/provisioned-by: everest-csi-provisioner
        name: pv-evs-example
      spec:
        accessModes:
        - ReadWriteOnce
        capacity:
          storage: 10Gi
        csi:
          driver: disk.csi.everest.io
          fsType: ext4
          volumeAttributes:
            everest.io/disk-mode: SCSI
            everest.io/disk-volume-type: SAS
            storage.kubernetes.io/csiProvisionerIdentity: everest-csi-provisioner
          volumeHandle: 0992dbda-6340-470e-a74e-4f0db288ed82
        persistentVolumeReclaimPolicy: Delete
        storageClassName: csi-disk

   Pay attention to the fields in bold and red. The parameters are described as follows:

   .. table:: **Table 1** EVS volume configuration parameters

      +------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                | Description                                                                                                                                        |
      +==========================================+====================================================================================================================================================+
      | failure-domain.beta.kubernetes.io/region | Region where the EVS disk is located. Use the same value as that of the FlexVolume PV.                                                             |
      +------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
      | failure-domain.beta.kubernetes.io/zone   | AZ where the EVS disk is located. Use the same value as that of the FlexVolume PV.                                                                 |
      +------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
      | name                                     | Name of the PV, which must be unique in the cluster.                                                                                               |
      +------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
      | storage                                  | EVS volume capacity in the unit of Gi. Use the value of **spec.capacity.storage** of the FlexVolume PV.                                            |
      +------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
      | driver                                   | Storage driver used to attach the volume. Set the driver to **disk.csi.everest.io** for the EVS volume.                                            |
      +------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
      | volumeHandle                             | Volume ID of the EVS disk. Use the value of **spec.flexVolume.options.volumeID** of the FlexVolume PV.                                             |
      +------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
      | everest.io/disk-mode                     | EVS disk mode. Use the value of **spec.flexVolume.options.disk-mode** of the FlexVolume PV.                                                        |
      +------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
      | everest.io/disk-volume-type              | EVS disk type. Use the value of **kubernetes.io/volumetype** in the storage class corresponding to **spec.storageClassName** of the FlexVolume PV. |
      +------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+
      | storageClassName                         | Name of the Kubernetes storage class associated with the storage volume. Set this field to **csi-disk** for EVS disks.                             |
      +------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+

   Configuration example of **a PV for an SFS volume**:

   .. code-block::

      apiVersion: v1
      kind: PersistentVolume
      metadata:
        name: pv-sfs-example
        annotations:
          pv.kubernetes.io/provisioned-by: everest-csi-provisioner
      spec:
        accessModes:
        - ReadWriteMany
        capacity:
          storage: 10Gi
        csi:
          driver: nas.csi.everest.io
          fsType: nfs
          volumeAttributes:
            everest.io/share-export-location:  # Shared path of the file storage
            storage.kubernetes.io/csiProvisionerIdentity: everest-csi-provisioner
          volumeHandle: 682f00bb-ace0-41d8-9b3e-913c9aa6b695
        persistentVolumeReclaimPolicy: Delete
        storageClassName: csi-nas

   Pay attention to the fields in bold and red. The parameters are described as follows:

   .. table:: **Table 2** SFS volume configuration parameters

      +----------------------------------+--------------------------------------------------------------------------------------------------------------------+
      | Parameter                        | Description                                                                                                        |
      +==================================+====================================================================================================================+
      | name                             | Name of the PV, which must be unique in the cluster.                                                               |
      +----------------------------------+--------------------------------------------------------------------------------------------------------------------+
      | storage                          | File storage size in the unit of Gi. Use the value of **spec.capacity.storage** of the FlexVolume PV.              |
      +----------------------------------+--------------------------------------------------------------------------------------------------------------------+
      | driver                           | Storage driver used to attach the volume. Set the driver to **nas.csi.everest.io** for the file system.            |
      +----------------------------------+--------------------------------------------------------------------------------------------------------------------+
      | everest.io/share-export-location | Shared path of the file system. Use the value of **spec.flexVolume.options.deviceMountPath** of the FlexVolume PV. |
      +----------------------------------+--------------------------------------------------------------------------------------------------------------------+
      | volumeHandle                     | File system ID. Use the value of **spec.flexVolume.options.volumeID** of the FlexVolume PV.                        |
      +----------------------------------+--------------------------------------------------------------------------------------------------------------------+
      | storageClassName                 | Name of the Kubernetes storage class. Set this field to **csi-nas**.                                               |
      +----------------------------------+--------------------------------------------------------------------------------------------------------------------+

   Configuration example of **a PV for an OBS volume**:

   .. code-block::

      apiVersion: v1
      kind: PersistentVolume
      metadata:
        name: pv-obs-example
        annotations:
          pv.kubernetes.io/provisioned-by: everest-csi-provisioner
      spec:
        accessModes:
        - ReadWriteMany
        capacity:
          storage: 1Gi
        csi:
          driver: obs.csi.everest.io
          fsType: s3fs
          volumeAttributes:
            everest.io/obs-volume-type: STANDARD
            everest.io/region: eu-de
            storage.kubernetes.io/csiProvisionerIdentity: everest-csi-provisioner
          volumeHandle: obs-normal-static-pv
        persistentVolumeReclaimPolicy: Delete
        storageClassName: csi-obs

   Pay attention to the fields in bold and red. The parameters are described as follows:

   .. table:: **Table 3** OBS volume configuration parameters

      +----------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                  | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
      +============================+===========================================================================================================================================================================================================================================================================================================================================================================================================================================================================================+
      | name                       | Name of the PV, which must be unique in the cluster.                                                                                                                                                                                                                                                                                                                                                                                                                                      |
      +----------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | storage                    | Storage capacity, in the unit of Gi. Set this parameter to the fixed value **1Gi**.                                                                                                                                                                                                                                                                                                                                                                                                       |
      +----------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | driver                     | Storage driver used to attach the volume. Set the driver to **obs.csi.everest.io** for the OBS volume.                                                                                                                                                                                                                                                                                                                                                                                    |
      +----------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | fsType                     | File type. Value options are **obsfs** or **s3fs**. If the value is **s3fs**, an OBS bucket is created and mounted using s3fs. If the value is **obsfs**, an OBS parallel file system is created and mounted using obsfs. Set this parameter according to the value of **spec.flexVolume.options.posix** of the FlexVolume PV. If the value of **spec.flexVolume.options.posix** is **true**, set this parameter to **obsfs**. If the value is **false**, set this parameter to **s3fs**. |
      +----------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | everest.io/obs-volume-type | Storage class, including **STANDARD** (standard bucket) and **WARM** (infrequent access bucket). Set this parameter according to the value of **spec.flexVolume.options.storage_class** of the FlexVolume PV. If the value of **spec.flexVolume.options.storage_class** is **standard**, set this parameter to **STANDARD**. If the value is **standard_ia**, set this parameter to **WARM**.                                                                                             |
      +----------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | everest.io/region          | Region where the OBS bucket is located. Use the value of **spec.flexVolume.options.region** of the FlexVolume PV.                                                                                                                                                                                                                                                                                                                                                                         |
      +----------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | volumeHandle               | OBS bucket name. Use the value of **spec.flexVolume.options.volumeID** of the FlexVolume PV.                                                                                                                                                                                                                                                                                                                                                                                              |
      +----------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | storageClassName           | Name of the Kubernetes storage class. Set this field to **csi-obs**.                                                                                                                                                                                                                                                                                                                                                                                                                      |
      +----------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   Configuration example of **a PV for an SFS Turbo volume**:

   .. code-block::

      apiVersion: v1
      kind: PersistentVolume
      metadata:
        name: pv-efs-example
        annotations:
          pv.kubernetes.io/provisioned-by: everest-csi-provisioner
      spec:
        accessModes:
        - ReadWriteMany
        capacity:
          storage: 10Gi
        csi:
          driver: sfsturbo.csi.everest.io
          fsType: nfs
          volumeAttributes:
            everest.io/share-export-location: 192.168.0.169:/
            storage.kubernetes.io/csiProvisionerIdentity: everest-csi-provisioner
          volumeHandle: 8962a2a2-a583-4b7f-bb74-fe76712d8414
        persistentVolumeReclaimPolicy: Delete
        storageClassName: csi-sfsturbo

   Pay attention to the fields in bold and red. The parameters are described as follows:

   .. table:: **Table 4** SFS Turbo volume configuration parameters

      +----------------------------------+-------------------------------------------------------------------------------------------------------------------------+
      | Parameter                        | Description                                                                                                             |
      +==================================+=========================================================================================================================+
      | name                             | Name of the PV, which must be unique in the cluster.                                                                    |
      +----------------------------------+-------------------------------------------------------------------------------------------------------------------------+
      | storage                          | File system size. Use the value of **spec.capacity.storage** of the FlexVolume PV.                                      |
      +----------------------------------+-------------------------------------------------------------------------------------------------------------------------+
      | driver                           | Storage driver used to attach the volume. Set it to **sfsturbo.csi.everest.io**.                                        |
      +----------------------------------+-------------------------------------------------------------------------------------------------------------------------+
      | everest.io/share-export-location | Shared path of the SFS Turbo volume. Use the value of **spec.flexVolume.options.deviceMountPath** of the FlexVolume PV. |
      +----------------------------------+-------------------------------------------------------------------------------------------------------------------------+
      | volumeHandle                     | SFS Turbo volume ID. Use the value of **spec.flexVolume.options.volumeID** of the FlexVolume PV.                        |
      +----------------------------------+-------------------------------------------------------------------------------------------------------------------------+
      | storageClassName                 | Name of the Kubernetes storage class. Set this field to **csi-sfsturbo** for SFS Turbo volumes.                         |
      +----------------------------------+-------------------------------------------------------------------------------------------------------------------------+

#. .. _cce_bestpractice_0107__li1710710385418:

   Configure a YAML file of the PVC in the CSI format according to the PVC in the FlexVolume format and associate the PVC with the PV created in :ref:`2 <cce_bestpractice_0107__li1219802032512>`.

   To be specific, run the following commands to configure the pvc-example.yaml file, which is used to create a PVC.

   **touch pvc-example.yaml**

   **vi pvc-example.yaml**

   Configuration example of **a PVC for an EVS volume**:

   .. code-block::

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        labels:
          failure-domain.beta.kubernetes.io/region: eu-de
          failure-domain.beta.kubernetes.io/zone: <zone name>
        annotations:
          everest.io/disk-volume-type: SAS
          volume.beta.kubernetes.io/storage-provisioner: everest-csi-provisioner
        name: pvc-evs-example
        namespace: default
      spec:
        accessModes:
        - ReadWriteOnce
        resources:
          requests:
            storage: 10Gi
        volumeName:  pv-evs-example
        storageClassName: csi-disk

   Pay attention to the fields in bold and red. The parameters are described as follows:

   .. table:: **Table 5** PVC configuration parameters for an EVS volume

      +------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                | Description                                                                                                                                                                                                                                |
      +==========================================+============================================================================================================================================================================================================================================+
      | failure-domain.beta.kubernetes.io/region | Region where the cluster is located. Use the same value as that of the FlexVolume PVC.                                                                                                                                                     |
      +------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | failure-domain.beta.kubernetes.io/zone   | AZ where the EVS disk is deployed. Use the same value as that of the FlexVolume PVC.                                                                                                                                                       |
      +------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | everest.io/disk-volume-type              | Storage class of the EVS disk. The value can be **SAS** or **SSD**. Set this parameter to the same value as that of the PV created in :ref:`2 <cce_bestpractice_0107__li1219802032512>`.                                                   |
      +------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | name                                     | PVC name, which must be unique in the namespace. The value must be unique in the namespace. (If the PVC is dynamically created by a stateful application, the value of this parameter must be the same as the name of the FlexVolume PVC.) |
      +------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | namespace                                | Namespace to which the PVC belongs. Use the same value as that of the FlexVolume PVC.                                                                                                                                                      |
      +------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | storage                                  | Requested capacity of the PVC, which must be the same as the storage size of the existing PV.                                                                                                                                              |
      +------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | volumeName                               | Name of the PV. Set this parameter to the name of the static PV in :ref:`2 <cce_bestpractice_0107__li1219802032512>`.                                                                                                                      |
      +------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | storageClassName                         | Name of the Kubernetes storage class. Set this field to **csi-disk** for EVS disks.                                                                                                                                                        |
      +------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   Configuration example of **a PVC for an SFS volume**:

   .. code-block::

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        annotations:
          volume.beta.kubernetes.io/storage-provisioner: everest-csi-provisioner
        name: pvc-sfs-example
        namespace: default
      spec:
        accessModes:
        - ReadWriteMany
        resources:
          requests:
            storage: 10Gi
        storageClassName: csi-nas
        volumeName: pv-sfs-example

   Pay attention to the fields in bold and red. The parameters are described as follows:

   .. table:: **Table 6** PVC configuration parameters for an SFS volume

      +------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter        | Description                                                                                                                                                                                                                                |
      +==================+============================================================================================================================================================================================================================================+
      | name             | PVC name, which must be unique in the namespace. The value must be unique in the namespace. (If the PVC is dynamically created by a stateful application, the value of this parameter must be the same as the name of the FlexVolume PVC.) |
      +------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | namespace        | Namespace to which the PVC belongs. Use the same value as that of the FlexVolume PVC.                                                                                                                                                      |
      +------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | storage          | Storage capacity, in the unit of Gi. The value must be the same as the storage size of the existing PV.                                                                                                                                    |
      +------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | storageClassName | Set this field to **csi-nas**.                                                                                                                                                                                                             |
      +------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | volumeName       | Name of the PV. Set this parameter to the name of the static PV in :ref:`2 <cce_bestpractice_0107__li1219802032512>`.                                                                                                                      |
      +------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   Configuration example of **a PVC for an OBS volume**:

   .. code-block::

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        annotations:
          volume.beta.kubernetes.io/storage-provisioner: everest-csi-provisioner
          everest.io/obs-volume-type: STANDARD
          csi.storage.k8s.io/fstype: s3fs
        name: pvc-obs-example
        namespace: default
      spec:
        accessModes:
        - ReadWriteMany
        resources:
          requests:
            storage: 1Gi
        storageClassName: csi-obs
        volumeName: pv-obs-example

   Pay attention to the fields in bold and red. The parameters are described as follows:

   .. table:: **Table 7** PVC configuration parameters for an OBS volume

      +----------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                  | Description                                                                                                                                                                                                                                |
      +============================+============================================================================================================================================================================================================================================+
      | everest.io/obs-volume-type | OBS volume type, which can be **STANDARD** (standard bucket) and **WARM** (infrequent access bucket). Set this parameter to the same value as that of the PV created in :ref:`2 <cce_bestpractice_0107__li1219802032512>`.                 |
      +----------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | csi.storage.k8s.io/fstype  | File type, which can be **obsfs** or **s3fs**. The value must be the same as that of **fsType** of the static OBS volume PV.                                                                                                               |
      +----------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | name                       | PVC name, which must be unique in the namespace. The value must be unique in the namespace. (If the PVC is dynamically created by a stateful application, the value of this parameter must be the same as the name of the FlexVolume PVC.) |
      +----------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | namespace                  | Namespace to which the PVC belongs. Use the same value as that of the FlexVolume PVC.                                                                                                                                                      |
      +----------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | storage                    | Storage capacity, in the unit of Gi. Set this parameter to the fixed value **1Gi**.                                                                                                                                                        |
      +----------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | storageClassName           | Name of the Kubernetes storage class. Set this field to **csi-obs**.                                                                                                                                                                       |
      +----------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | volumeName                 | Name of the PV. Set this parameter to the name of the static PV created in :ref:`2 <cce_bestpractice_0107__li1219802032512>`.                                                                                                              |
      +----------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   Configuration example of **a PVC for an SFS Turbo volume**:

   .. code-block::

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        annotations:
          volume.beta.kubernetes.io/storage-provisioner: everest-csi-provisioner
        name: pvc-efs-example
        namespace: default
      spec:
        accessModes:
        - ReadWriteMany
        resources:
          requests:
            storage: 10Gi
        storageClassName: csi-sfsturbo
        volumeName: pv-efs-example

   Pay attention to the fields in bold and red. The parameters are described as follows:

   .. table:: **Table 8** PVC configuration parameters for an SFS Turbo volume

      +------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter        | Description                                                                                                                                                                                                                                |
      +==================+============================================================================================================================================================================================================================================+
      | name             | PVC name, which must be unique in the namespace. The value must be unique in the namespace. (If the PVC is dynamically created by a stateful application, the value of this parameter must be the same as the name of the FlexVolume PVC.) |
      +------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | namespace        | Namespace to which the PVC belongs. Use the same value as that of the FlexVolume PVC.                                                                                                                                                      |
      +------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | storageClassName | Name of the Kubernetes storage class. Set this field to **csi-sfsturbo**.                                                                                                                                                                  |
      +------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | storage          | Storage capacity, in the unit of Gi. The value must be the same as the storage size of the existing PV.                                                                                                                                    |
      +------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | volumeName       | Name of the PV. Set this parameter to the name of the static PV created in :ref:`2 <cce_bestpractice_0107__li1219802032512>`.                                                                                                              |
      +------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. .. _cce_bestpractice_0107__li487255772614:

   Upgrade the workload to use a new PVC.

   **For Deployments**

   a. Run the **kubectl create -f** commands to create a PV and PVC.

      **kubectl create -f pv-example.yaml**

      **kubectl create -f pvc-example.yaml**

      .. note::

         Replace the example file name **pvc-example.yaml** in the preceding commands with the names of the YAML files configured in :ref:`2 <cce_bestpractice_0107__li1219802032512>` and :ref:`3 <cce_bestpractice_0107__li1710710385418>`.

   b. Go to the CCE console. On the workload upgrade page, click **Upgrade** > **Advanced Settings** > **Data Storage** > **Cloud Storage**.

   c. Uninstall the old storage and add the PVC in the CSI format. Retain the original mounting path in the container.

   d. Click **Submit**.

   e. Wait until the pods are running.

   **For StatefulSets that use existing storage**

   a. Run the **kubectl create -f** commands to create a PV and PVC.

      **kubectl create -f pv-example.yaml**

      **kubectl create -f pvc-example.yaml**

      .. note::

         Replace the example file name **pvc-example.yaml** in the preceding commands with the names of the YAML files configured in :ref:`2 <cce_bestpractice_0107__li1219802032512>` and :ref:`3 <cce_bestpractice_0107__li1710710385418>`.

   b. Run the **kubectl edit** command to edit the StatefulSet and use the newly created PVC.

      **kubectl edit sts sts-example -n** xxx

      |image1|

      .. note::

         Replace **sts-example** in the preceding command with the actual name of the StatefulSet to upgrade. **xxx** indicates the namespace to which the StatefulSet belongs.

   c. Wait until the pods are running.

   .. note::

      The current console does not support the operation of adding new cloud storage for StatefulSets. Use the kubectl commands to replace the storage with the newly created PVC.

   **For StatefulSets that use dynamically allocated storage**

   a. Back up the PV and PVC in the flexVolume format used by the StatefulSet.

      **kubectl get pvc xxx -n {namespaces} -oyaml > pvc-backup.yaml**

      **kubectl get pv xxx -n {namespaces} -oyaml > pv-backup.yaml**

   b. Change the number of pods to **0**.

   c. On the storage page, disassociate the flexVolume PVC used by the StatefulSet.

   d. Run the **kubectl create -f** commands to create a PV and PVC.

      **kubectl create -f pv-example.yaml**

      **kubectl create -f pvc-example.yaml**

      .. note::

         Replace the example file name **pvc-example.yaml** in the preceding commands with the names of the YAML files configured in :ref:`2 <cce_bestpractice_0107__li1219802032512>` and :ref:`3 <cce_bestpractice_0107__li1710710385418>`.

   e. Change the number of pods back to the original value and wait until the pods are running.

   .. note::

      The dynamic allocation of storage for StatefulSets is achieved by using **volumeClaimTemplates**. This field cannot be modified by Kubernetes. Therefore, data cannot be migrated by using a new PVC.

      The PVC naming rule of the **volumeClaimTemplates** is fixed. When a PVC that meets the naming rule exists, this PVC is used.

      Therefore, disassociate the original PVC first, and then create a PVC with the same name in the CSI format.

   6. (Optional) Recreate the stateful application to ensure that a CSI PVC is used when the application is scaled out. Otherwise, FlexVolume PVCs are used in scaling out.

   -  Run the following command to obtain the YAML file of the StatefulSet:

   **kubectl get sts xxx -n {namespaces} -oyaml > sts.yaml**

   -  Run the following command to back up the YAML file of the StatefulSet:

   **cp sts.yaml sts-backup.yaml**

   -  Modify the definition of **volumeClaimTemplates** in the YAML file of the StatefulSet.

   **vi sts.yaml**

   Configuration example of **volumeClaimTemplates for an EVS volume**:

   .. code-block::

        volumeClaimTemplates:
          - metadata:
              name: pvc-161070049798261342
              namespace: default
              creationTimestamp: null
              annotations:
                everest.io/disk-volume-type: SAS
            spec:
              accessModes:
                - ReadWriteOnce
              resources:
                requests:
                  storage: 10Gi
              storageClassName: csi-disk

   The parameter value must be the same as the PVC of the EVS volume created in :ref:`3 <cce_bestpractice_0107__li1710710385418>`.

   Configuration example of **volumeClaimTemplates for an SFS volume**:

   .. code-block::

        volumeClaimTemplates:
          - metadata:
              name: pvc-161063441560279697
              namespace: default
              creationTimestamp: null
            spec:
              accessModes:
                - ReadWriteMany
              resources:
                requests:
                  storage: 10Gi
              storageClassName: csi-nas

   The parameter value must be the same as the PVC of the SFS volume created in :ref:`3 <cce_bestpractice_0107__li1710710385418>`.

   Configuration example of **volumeClaimTemplates for an OBS volume**:

   .. code-block::

        volumeClaimTemplates:
          - metadata:
              name: pvc-161070100417416148
              namespace: default
              creationTimestamp: null
              annotations:
                csi.storage.k8s.io/fstype: s3fs
                everest.io/obs-volume-type: STANDARD
            spec:
              accessModes:
                - ReadWriteMany
              resources:
                requests:
                  storage: 1Gi
              storageClassName: csi-obs

   The parameter value must be the same as the PVC of the OBS volume created in :ref:`3 <cce_bestpractice_0107__li1710710385418>`.

   -  Delete the StatefulSet.

   **kubectl delete sts xxx -n** {namespaces}

   -  Create the StatefulSet.

   **kubectl create -f sts.yaml**

#. Check service functions.

   a. Check whether the application is running properly.
   b. Checking whether the data storage is normal.

   .. note::

      If a rollback is required, perform :ref:`4 <cce_bestpractice_0107__li487255772614>`. Select the PVC in FlexVolume format and upgrade the application.

#. Uninstall the PVC in the FlexVolume format.

   If the application functions normally, unbind the PVC in the FlexVolume format on the storage management page.

   You can also run the kubectl command to delete the PVC and PV of the FlexVolume format.

   .. caution::

      Before deleting a PV, change the persistentVolumeReclaimPolicy of the PV to **Retain**. Otherwise, the underlying storage will be reclaimed after the PV is deleted.

      If the cluster has been upgraded before the storage migration, PVs may fail to be deleted. You can remove the PV protection field **finalizers** to delete PVs.

      kubectl patch pv {pv_name} -p '{"metadata":{"finalizers":null}}'

.. |image1| image:: /_static/images/en-us_image_0000001851585440.png
