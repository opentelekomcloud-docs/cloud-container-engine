:original_name: cce_01_0211.html

.. _cce_01_0211:

Snapshots and Backups
=====================

CCE works with EVS to support snapshots. A snapshot is a complete copy or image of EVS disk data at a certain point of time, which can be used for data DR.

You can create snapshots to rapidly save the disk data at specified time points. In addition, you can use snapshots to create new disks so that the created disks will contain the snapshot data in the beginning.

Precautions
-----------

-  The snapshot function is available **only for clusters of v1.15 or later** and requires the CSI-based everest add-on.
-  The subtype (common I/O, high I/O, or ultra-high I/O), disk mode (SCSI or VBD), data encryption, sharing status, and capacity of an EVS disk created from a snapshot must be the same as those of the disk associated with the snapshot. These attributes cannot be modified after being queried or set.
-  Snapshots can be created only for available or in-use CSI disks. During the free trial, you can create up to 7 snapshots per disk.
-  Snapshot data of encrypted disks is stored encrypted, and that of non-encrypted disks is stored non-encrypted.

Application Scenario
--------------------

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

Creating a Snapshot
-------------------

**Using the CCE Console**

#. Log in to the CCE console.
#. Go to the cluster details page, choose **Storage** from the navigation pane, and click the **Snapshots and Backups** tab.
#. Click **Create Snapshot** in the upper right corner. In the dialog box displayed, set related parameters.

   -  **Snapshot Name**: Enter a snapshot name.
   -  **Storage**: Select the PVC for which you want to create a snapshot.

#. Click **Create**.

**Using YAML**

.. code-block::

   kind: VolumeSnapshot
   apiVersion: snapshot.storage.k8s.io/v1beta1
   metadata:
     finalizers:
       - snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
       - snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
     name: cce-disksnap-test
     namespace: default
   spec:
     source:
       persistentVolumeClaimName: pvc-evs-test     # PVC name. Only an EVS PVC can be created.
     volumeSnapshotClassName: csi-disk-snapclass

Using a Snapshot to Creating a PVC
----------------------------------

The disk type, encryption setting, and disk mode of the created EVS PVC are consistent with those of the snapshot's source EVS disk.

**Using the CCE Console**

#. Log in to the CCE console.
#. Go to the cluster details page, choose **Storage** from the navigation pane, and click the **PersistentVolumeClaims (PVCs)** tab.
#. Click **Create PVC** in the upper right corner. In the dialog box displayed, set the PVC parameters.

   -  **Creation Mode**: Select **Snapshot**.
   -  **PVC Name**: name of a PVC.
   -  **Snapshot**: Select the snapshot to be used.

#. Click **Create**.

**Using YAML**

.. code-block::

   apiVersion: v1
   kind: PersistentVolumeClaim
   metadata:
     name: pvc-test
     namespace: default
     annotations:
       everest.io/disk-volume-type: SSD     # EVS disk type, which must be the same as that of the source EVS disk of the snapshot.
     labels:
       failure-domain.beta.kubernetes.io/region: eu-de
       failure-domain.beta.kubernetes.io/zone: eu-de-01
   spec:
     accessModes:
     - ReadWriteOnce
     resources:
       requests:
         storage: '10'
     storageClassName: csi-disk
     dataSource:
       name: cce-disksnap-test             # Snapshot name
       kind: VolumeSnapshot
       apiGroup: snapshot.storage.k8s.io
