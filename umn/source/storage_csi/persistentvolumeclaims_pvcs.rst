:original_name: cce_01_0378.html

.. _cce_01_0378:

PersistentVolumeClaims (PVCs)
=============================

A PVC describes a workload's request for storage resources. This request consumes existing PVs in the cluster. If there is no PV available, underlying storage and PVs are dynamically created. When creating a PVC, you need to describe the attributes of the requested persistent storage, such as the size of the volume and the read/write permissions.

Notes and Constraints
---------------------

When a PVC is created, the system checks whether there is an available PV with the same configuration in the cluster. If yes, the PVC binds the available PV to the cluster. If no PV meets the matching conditions, the system dynamically creates a storage volume.

+---------------+-------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------+
| Description   | PVC Field                                                                                       | PV Field                                                                                       | Matching Logic                                                                        |
+===============+=================================================================================================+================================================================================================+=======================================================================================+
| region        | pvc.metadata.labels (failure-domain.beta.kubernetes.io/region or topology.kubernetes.io/region) | pv.metadata.labels (failure-domain.beta.kubernetes.io/region or topology.kubernetes.io/region) | Defined or not defined at the same time. If defined, the settings must be consistent. |
+---------------+-------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------+
| zone          | pvc.metadata.labels (failure-domain.beta.kubernetes.io/zone or topology.kubernetes.io/zone)     | pv.metadata.labels (failure-domain.beta.kubernetes.io/zone or topology.kubernetes.io/zone)     | Defined or not defined at the same time. If defined, the settings must be consistent. |
+---------------+-------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------+
| EVS disk type | pvc.metadata.annotations (everest.io/disk-volume-type)                                          | pv.spec.csi.volumeAttributes (everest.io/disk-volume-type)                                     | Defined or not defined at the same time. If defined, the settings must be consistent. |
+---------------+-------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------+
| Key ID        | pvc.metadata.annotations (everest.io/crypt-key-id)                                              | pv.spec.csi.volumeAttributes (everest.io/crypt-key-id)                                         | Defined or not defined at the same time. If defined, the settings must be consistent. |
+---------------+-------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------+
| accessMode    | accessMode                                                                                      | accessMode                                                                                     | The settings must be consistent.                                                      |
+---------------+-------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------+
| Storage class | storageclass                                                                                    | storageclass                                                                                   | The settings must be consistent.                                                      |
+---------------+-------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------+

.. _cce_01_0378__en-us_topic_0000001199501146_section43881411172418:

Volume Access Modes
-------------------

PVs can be mounted to the host system only in the mode supported by underlying storage resources. For example, a file storage system can be read and written by multiple nodes, but an EVS disk can be read and written by only one node.

-  ReadWriteOnce: A volume can be mounted as read-write by a single node. This access mode is supported by EVS.
-  ReadWriteMany: A volume can be mounted as read-write by multiple nodes. This access mode is supported by SFS, SFS Turbo, and OBS.

.. table:: **Table 1** Supported access modes

   ============ ============= =============
   Storage Type ReadWriteOnce ReadWriteMany
   ============ ============= =============
   EVS          Y             x
   SFS          x             Y
   OBS          x             Y
   SFS Turbo    x             Y
   ============ ============= =============

Using a Storage Class to Create a PVC
-------------------------------------

StorageClass describes the storage class used in the cluster. You need to specify StorageClass to dynamically create PVs and underlying storage resources when creating a PVC.

**Using the CCE Console**

#. Log in to the CCE console.
#. Go to the cluster details page, choose **Storage** from the navigation pane, and click the **PersistentVolumeClaims (PVCs)** tab.
#. Click **Create PVC** in the upper right corner. In the dialog box displayed, set the PVC parameters.

   -  **Creation Method**: Select **Storage class**.
   -  **PVC Name**: Enter a PVC name.
   -  **Storage Class**: Select the required storage class. The following storage resources can be dynamically provisioned:

      -  **csi-disk**: EVS disk.
      -  **csi-obs**: OBS bucket.

   -  **AZ** (supported only by EVS): Select the AZ where the EVS disk is located.
   -  **Disk Type**: Select an EVS disk type. EVS disk types vary in different regions.

      -  Common I/O
      -  High I/O
      -  Ultra-high I/O

   -  **Access Mode**: **ReadWriteOnce** and **ReadWriteMany** are supported. For details, see :ref:`Volume Access Modes <cce_01_0378__en-us_topic_0000001199501146_section43881411172418>`.
   -  **Capacity (GiB)** (supported only by EVS and SFS): storage capacity. This parameter is not available for OBS.
   -  **Encryption** (supported only for EVS and SFS): Select **Encryption**. After selecting this option, you need to select a key.
   -  **Secret** (supported only for OBS): Select an access key for OBS. For details, see :ref:`Using a Custom AK/SK to Mount an OBS Volume <cce_01_0336>`.

#. Click **Create**.

**Using YAML**

Example YAML for EVS

-  **failure-domain.beta.kubernetes.io/region**: region where the cluster is located.

   For details about the value of **region**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.

-  **failure-domain.beta.kubernetes.io/zone**: AZ where the EVS volume is created. It must be the same as the AZ planned for the workload.

   For details about the value of **zone**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.

.. code-block::

   apiVersion: v1
   kind: PersistentVolumeClaim
   metadata:
     name: pvc-evs-auto-example
     namespace: default
     annotations:
       everest.io/disk-volume-type: SSD    # EVS disk type.
       everest.io/crypt-key-id: 0992dbda-6340-470e-a74e-4f0db288ed82  # (Optional) Key ID. The key is used to encrypt EVS disks.

     labels:
       failure-domain.beta.kubernetes.io/region: eu-de
       failure-domain.beta.kubernetes.io/zone: eu-de-01
   spec:
     accessModes:
     - ReadWriteOnce               # The value must be ReadWriteOnce for EVS.
     resources:
       requests:
         storage: 10Gi             # EVS disk capacity, ranging from 1 to 32768.
     storageClassName: csi-disk    # The storage class type is EVS.

Example YAML for OBS:

.. code-block::

   apiVersion: v1
   kind: PersistentVolumeClaim
   metadata:
     name: obs-warm-provision-pvc
     namespace: default
     annotations:
       everest.io/obs-volume-type: STANDARD      # OBS bucket type. Currently, standard (STANDARD) and infrequent access (WARM) are supported.
       csi.storage.k8s.io/fstype: obsfs          # File type. obsfs indicates to create a parallel file system (recommended), and s3fs indicates to create an OBS bucket.

   spec:
     accessModes:
     - ReadWriteMany             # The value must be ReadWriteMany for OBS.
     resources:
       requests:
         storage: 1Gi                 # This field is valid only for verification (fixed to 1, cannot be empty or 0). The value setting does not take effect for OBS buckets.
     storageClassName: csi-obs        # The storage class type is OBS.

Using a PV to Create a PVC
--------------------------

If a PV has been created, you can create a PVC to apply for PV resources.

**Using the CCE Console**

#. Log in to the CCE console.
#. Go to the cluster details page, choose **Storage** from the navigation pane, and click the **PersistentVolumeClaims (PVCs)** tab.
#. Click **Create PVC** in the upper right corner. In the dialog box displayed, set the PVC parameters.

   -  **Creation Method**: Select **Existing volume**.
   -  **PVC Name**: Enter a PVC name.
   -  **Volume Type**: Select your required volume type.

      -  EVS
      -  SFS
      -  OBS
      -  SFS Turbo

   -  **Associate Volume**: Select the volume to be associated, that is, the PV.

#. Click **Create**.

**Using YAML**

Example YAML for EVS

-  **failure-domain.beta.kubernetes.io/region**: region where the cluster is located.

   For details about the value of **region**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.

-  **failure-domain.beta.kubernetes.io/zone**: AZ where the EVS volume is created. It must be the same as the AZ planned for the workload.

   For details about the value of **zone**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.

.. code-block::

   apiVersion: v1
   kind: PersistentVolumeClaim
   metadata:
     name: pvc-test
     namespace: default
     annotations:
       everest.io/disk-volume-type: SAS                                # EVS disk type.
       everest.io/crypt-key-id: fe0757de-104c-4b32-99c5-ee832b3bcaa3   # (Optional) Key ID. The key is used to encrypt EVS disks.
       volume.beta.kubernetes.io/storage-provisioner: everest-csi-provisioner

     labels:
       failure-domain.beta.kubernetes.io/region: eu-de
       failure-domain.beta.kubernetes.io/zone: eu-de-01
   spec:
     accessModes:
     - ReadWriteOnce               # The value must be ReadWriteOnce for EVS.
     resources:
       requests:
         storage: 10Gi
     storageClassName: csi-disk     # Storage class name. The value is csi-disk for EVS.
     volumeName: cce-evs-test       # PV name.

Example YAML for SFS:

.. code-block::

   apiVersion: v1
   kind: PersistentVolumeClaim
   metadata:
     name: pvc-sfs-test
     namespace: default
     annotations:
       volume.beta.kubernetes.io/storage-provisioner: everest-csi-provisioner
   spec:
     accessModes:
     - ReadWriteMany              # The value must be ReadWriteMany for SFS.
     resources:
       requests:
         storage: 100Gi           # Requested PVC capacity.
     storageClassName: csi-nas    # Storage class name. The value is csi-nas for SFS.
     volumeName: cce-sfs-test     # PV name.

Example YAML for OBS:

.. code-block::

   apiVersion: v1
   kind: PersistentVolumeClaim
   metadata:
     name: pvc-obs-test
     namespace: default
     annotations:
       everest.io/obs-volume-type: STANDARD                         # OBS bucket type. Currently, standard (STANDARD) and infrequent access (WARM) are supported.
       csi.storage.k8s.io/fstype: s3fs                              # File type. obsfs indicates to create a parallel file system (recommended), and s3fs indicates to create an OBS bucket.
       csi.storage.k8s.io/node-publish-secret-name: test-user
       csi.storage.k8s.io/node-publish-secret-namespace: default
       volume.beta.kubernetes.io/storage-provisioner: everest-csi-provisioner

   spec:
     accessModes:
     - ReadWriteMany             # The value must be ReadWriteMany for OBS.
     resources:
       requests:
         storage: 1Gi            # Requested PVC capacity. This field is valid only for verification (fixed to 1, cannot be empty or 0). The value setting does not take effect for OBS buckets.
     storageClassName: csi-obs   # Storage class name. The value is csi-obs for OBS.
     volumeName: cce-obs-test    # PV name.

Example YAML for SFS Turbo:

.. code-block::

   apiVersion: v1
   kind: PersistentVolumeClaim
   metadata:
     name: pvc-test
     namespace: default
     annotations:
       volume.beta.kubernetes.io/storage-provisioner: everest-csi-provisioner
   spec:
     accessModes:
       - ReadWriteMany               # The value must be ReadWriteMany for SFS Turbo.
     resources:
       requests:
         storage: 100Gi              # Requested PVC capacity.
     storageClassName: csi-sfsturbo  # Storage class name. The value is csi-sfsturbo for SFS Turbo.
     volumeName: pv-sfsturbo-test         # PV name.

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
       failure-domain.beta.kubernetes.io/zone:
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
