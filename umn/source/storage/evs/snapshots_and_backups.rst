:original_name: cce_10_0381.html

.. _cce_10_0381:

Snapshots and Backups
=====================

CCE works with EVS to support snapshots. A snapshot is a complete copy or image of EVS disk data at a specific time, which can be used for data DR.

You can create snapshots to rapidly save the disk data at a specific time. In addition, you can use snapshots to create disks so that the created disks will contain the snapshot data in the beginning.

Precautions
-----------

-  The snapshot function is available **only for clusters of v1.15 or later** and requires the CSI-based Everest add-on.
-  The subtype (common I/O, high I/O, or ultra-high I/O), disk mode (VBD or SCSI), data encryption, sharing status, and capacity of an EVS disk created from a snapshot must be the same as those of the disk associated with the snapshot. These attributes cannot be modified after being checked or configured.
-  Snapshots can be created only for EVS disks that are available or in use, and a maximum of seven snapshots can be created for a single EVS disk.
-  Snapshots can be created only for PVCs created using the storage class (whose name starts with csi) provided by the Everest add-on. Snapshots cannot be created for PVCs created using the FlexVolume storage class whose name is ssd, sas, or sata.
-  Snapshot data of encrypted disks is stored encrypted, and that of non-encrypted disks is stored non-encrypted.
-  A PVC of the xfs file system type can generate snapshots. The file system of the disk associated with the PVC created using these snapshots remains xfs.

Application Scenarios
---------------------

The snapshot feature helps address your following needs:

-  **Routine data backup**

   You can create snapshots for EVS disks regularly and use snapshots to recover your data in case that data loss or data inconsistency occurred due to misoperations, viruses, or attacks.

-  **Rapid data restoration**

   You can create a snapshot or multiple snapshots before an OS change, application software upgrade, or a service data migration. If an exception occurs during the upgrade or migration, service data can be rapidly restored to the time point when the snapshot was created.

   For example, a fault occurred on system disk A of ECS A, and therefore ECS A cannot be started. Because system disk A is already faulty, the data on system disk A cannot be restored by rolling back snapshots. In this case, you can use an existing snapshot of system disk A to create EVS disk B and attach it to ECS B that is running properly. Then, ECS B can read data from system disk A using EVS disk B.

   .. note::

      The snapshot capability provided by CCE is the same as the CSI snapshot function provided by the Kubernetes community. EVS disks can be created only based on snapshots, and snapshots cannot be rolled back to source EVS disks.

-  **Rapid deployment of multiple services**

   You can use a snapshot to create multiple EVS disks containing the same initial data, and these disks can be used as data resources for various services, for example, data mining, report query, and development and testing. This method protects the initial data and creates disks rapidly, meeting the diversified service data requirements.

Creating a Snapshot Through the Console
---------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Storage**. Then, click the **Snapshots and Backups** tab on the right.
#. Click **Create Snapshot** in the upper right corner. In the dialog box displayed, configure parameters.

   -  **Snapshot Name**: Enter a snapshot name.
   -  **Storage**: Select a PVC. Only the EVS type is available.

#. Click **Create**.

Creating a Snapshot Using kubectl
---------------------------------

#. Use kubectl to access the cluster.

#. Create **snapshot.yaml** for a snapshot. The file name is customizable.

   .. code-block::

      vim snapshot.yaml

   The file content is as follows:

   .. code-block::

      kind: VolumeSnapshot
      apiVersion: snapshot.storage.k8s.io/v1beta1
      metadata:
        name: cce-disksnap-test   # The snapshot name, which is customizable
        namespace: default
      spec:
        source:
          persistentVolumeClaimName: pvc-evs-test     # The PVC name. Only EVS PVCs can be selected.
        volumeSnapshotClassName: csi-disk-snapclass

#. Create the snapshot. Parameter **-n** indicates the namespace. You can replace **default** with the target namespace.

   .. code-block::

      kubectl apply -f snapshot.yaml -n default

   Information similar to the following is displayed:

   .. code-block::

      volumesnapshot.snapshot.storage.k8s.io/cce-disksnap-test created

#. Check whether the snapshot has been created.

   .. code-block::

      kubectl get volumesnapshot -n default

   If the value of **READYTOUSE** is **true**, the snapshot has been created.

   .. code-block::

      NAME                 READYTOUSE    SOURCEPVC       SOURCESNAPSHOTCONTENT     RESTORESIZE   SNAPSHOTCLASS         ...
      cce-disksnap-test    true          pvc-evs-test    <none>                    10Gi          csi-disk-snapclass    ...

Creating a PVC Through the Console
----------------------------------

After creating a snapshot, you can use it to create an EVS PVC. The operations described in this section are performed through the console. Ensure the disk type, encryption setting, and disk mode are consistent with those of the snapshot's source EVS disk.

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Storage**. Then, click the **Snapshots and Backups** tab on the right.
#. Locate the snapshot that you want to use for creating a PVC, click **Create PVC**, and configure PVC parameters in the displayed dialog box.

   .. table:: **Table 1** PVC parameters

      +---------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                             | Example Value         | Description                                                                                                                                                                                                                                                                                                                                                                               |
      +=======================================+=======================+===========================================================================================================================================================================================================================================================================================================================================================================================+
      | PVC Name                              | pvc-test              | The PVC name.                                                                                                                                                                                                                                                                                                                                                                             |
      |                                       |                       |                                                                                                                                                                                                                                                                                                                                                                                           |
      |                                       |                       | Enter 1 to 63 characters, including the prefix. Use only lowercase letters, digits, and hyphens (-). Do not start or end with a hyphen.                                                                                                                                                                                                                                                   |
      +---------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | (Optional) Storage Volume Name Prefix | N/A                   | The name prefix for the underlying storage that is automatically created. The actual underlying storage name will be in the format of "Storage volume name prefix + PVC UID". If this parameter is left blank, the default prefix **pvc** will be used. For example, if the storage volume name prefix is set to **test**, the actual underlying storage name will be **test-**\ *{UID}*. |
      |                                       |                       |                                                                                                                                                                                                                                                                                                                                                                                           |
      |                                       |                       | Available only when the cluster version is v1.23.14-r0, v1.25.9-r0, v1.27.6-r0, v1.28.4-r0, or later, and Everest v2.4.15 or later is installed in the cluster.                                                                                                                                                                                                                           |
      +---------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Resource Tag                          | N/A                   | You can add resource tags to classify resources. This function is available only when the Everest version in the cluster is 2.1.39 or later.                                                                                                                                                                                                                                              |
      |                                       |                       |                                                                                                                                                                                                                                                                                                                                                                                           |
      |                                       |                       | You can create predefined tags on the TMS console. These tags are available to all resources that support tags. You can use these tags to improve the tag creation and resource migration efficiency.                                                                                                                                                                                     |
      |                                       |                       |                                                                                                                                                                                                                                                                                                                                                                                           |
      |                                       |                       | CCE automatically creates system tags **CCE-Cluster-ID=**\ *<cluster-ID>*, **CCE-Cluster-Name=**\ *<cluster-name>*, and **CCE-Namespace=**\ *<namespace-name>*. These tags cannot be modified.                                                                                                                                                                                            |
      +---------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **Create**.

Creating a PVC Using kubectl
----------------------------

After creating a snapshot, you can use it to create an EVS PVC. The operations described in this section are performed using kubectl. Ensure the disk type, encryption setting, and disk mode are consistent with those of the snapshot's source EVS disk.

#. Use kubectl to access the cluster.

#. Create **pvc-test.yaml** and use a snapshot to create a PVC. The file name is customizable.

   .. code-block::

      vim pvc-test.yaml

   The file content is as follows (For details about PVCs, see :ref:`Dynamically Creating an EVS Disk Using kubectl <cce_10_0615__section189114109321>`):

   .. code-block::

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        name: pvc-test
        namespace: default
        annotations:
          everest.io/disk-volume-type: SSD     # EVS disk type, which must be the same as that of the snapshot's source EVS disk.
          everest.io/disk-volume-tags: '{"key1":"value1","key2":"value2"}' # (Optional) Custom resource tags
          csi.storage.k8s.io/fstype: xfs    # (Optional) Configure this field when the snapshot file system is of the xfs type.
        labels:
          failure-domain.beta.kubernetes.io/region: <your_region>   # Region where the EVS disk is located
          failure-domain.beta.kubernetes.io/zone: <your_zone>       # AZ where the EVS disk is located
      spec:
        accessModes:
        - ReadWriteOnce
        resources:
          requests:
            storage: 10Gi
        storageClassName: csi-disk
        dataSource:
          name: cce-disksnap-test             # The snapshot name
          kind: VolumeSnapshot
          apiGroup: snapshot.storage.k8s.io

#. Create the PVC. Parameter **-n** indicates the namespace. You can replace **default** with the target namespace.

   .. code-block::

      kubectl apply -f pvc-test.yaml -n default

   Information similar to the following is displayed:

   .. code-block::

      persistentvolumeclaim/pvc-test created

#. Check whether the PVC has been created.

   .. code-block::

      kubectl get pvc pvc-test

   If **STATUS** is **Bound**, the PVC has been created.

   .. code-block::

      NAME      STATUS   VOLUME                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
      pvc-test   Bound    pvc-7b9f4c8d-1a2b-3c4d-5e6f-7g8h9i0jkl   10Gi     RWO         csi-disk     1m
