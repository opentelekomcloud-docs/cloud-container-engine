:original_name: cce_10_0379.html

.. _cce_10_0379:

PersistentVolumes (PVs)
=======================

A PV is a persistent storage volume in a cluster. Same as a node, a PV is a cluster-level resource.

Notes and Constraints
---------------------

-  On the new CCE console (the cluster needs to be **upgraded to v1.19.10 or later** and **the everest add-on needs to be upgraded to v1.2.10 or later**), PVs are open to you for management. On the old CCE console, PVs can only be imported or dynamically created. You cannot manage the PV lifecycle on the console.
-  Multiple PVs can use the same SFS or SFS Turbo file system with the following restrictions:

   -  An error may occur if multiple PVCs/PVs that use the same underlying SFS or SFS Turbo file system are mounted to the same pod.
   -  The **persistentVolumeReclaimPolicy** parameter in the PVs must be set to **Retain**. Otherwise, when a PV is deleted, the associated underlying volume may be deleted. In this case, other PVs associated with the underlying volume may be abnormal.
   -  When the underlying volume is repeatedly used, it is recommended that ReadWriteMany be implemented at the application layer to prevent data overwriting and loss.

Volume Access Modes
-------------------

PVs can be mounted to the host system only in the mode supported by underlying storage resources. For example, a file storage system can be read and written by multiple nodes, but an EVS disk can be read and written by only one node.

-  ReadWriteOnce: A volume can be mounted as read-write by a single node. This access mode is supported by EVS.
-  ReadWriteMany: A volume can be mounted as read-write by multiple nodes. This access mode is supported by SFS, OBS, and SFS Turbo.

.. table:: **Table 1** Access modes supported by cloud storage

   ============ ============= =============
   Storage Type ReadWriteOnce ReadWriteMany
   ============ ============= =============
   EVS          Y             x
   SFS          x             Y
   OBS          x             Y
   SFS Turbo    x             Y
   ============ ============= =============

.. _cce_10_0379__section19999142414413:

PV Reclaim Policy
-----------------

A PV reclaim policy is used to delete or reclaim underlying volumes when a PVC is deleted. The value can be **Delete** or **Retain**.

-  **Delete**: When a PVC is deleted, the PV and underlying storage resources are deleted.
-  **Retain**: When a PVC is deleted, the PV and underlying storage resources are not deleted. Instead, you must manually delete these resources. After a PVC is deleted, the PV resource is in the Released state and cannot be bound to the PVC again.

Everest also allows you to delete a PVC without deleting underlying storage resources. This function can be achieved only by using a YAML file. Set the PV reclaim policy to **Delete** and add **annotations"everest.io/reclaim-policy: retain-volume-only"**. In this way, when the PVC is deleted, the PV resource is deleted, but the underlying storage resources are retained.

Creating an EVS Volume
----------------------

.. note::

   The requirements for creating an EVS volume are as follows:

   -  System disks, DSS disks, and shared disks cannot be used.
   -  The EVS disk is one of the supported types (common I/O, high I/O, and ultra-high I/O), and the EVS disk device type is SCSI.
   -  The EVS disk is not frozen or used, and the status is available.
   -  If the EVS disk is encrypted, the key must be available.

**Using the CCE Console**

#. Log in to the CCE console.
#. Click the cluster name and access the cluster console. Choose **Storage** from the navigation pane, and click the **PersistentVolumes (PVs)** tab.
#. Click **Create Volume** in the upper right corner. In the dialog box displayed, set the volume parameters.

   -  **Volume Type**: Select **EVS**.
   -  **EVS**:
   -  **PV Name**: Enter a PV name.
   -  **Access Mode**: ReadWriteOnce
   -  **Reclaim Policy**: Select **Delete** or **Retain** as required. For details, see :ref:`PV Reclaim Policy <cce_10_0379__section19999142414413>`.

#. Click **Create**.

**Using YAML**

.. code-block::

   apiVersion: v1
   kind: PersistentVolume
   metadata:
     annotations:
       pv.kubernetes.io/provisioned-by: everest-csi-provisioner
       everest.io/reclaim-policy: retain-volume-only         # (Optional) The PV is deleted while the underlying volume is retained.
     name: cce-evs-test
     labels:
       failure-domain.beta.kubernetes.io/region: eu-de
       failure-domain.beta.kubernetes.io/zone:
   spec:
     accessModes:
       - ReadWriteOnce     # Access mode. The value is fixed to ReadWriteOnce for EVS.
     capacity:
       storage: 10Gi       #  EVS disk capacity, in the unit of Gi. The value ranges from 1 to 32768.
     csi:
       driver: disk.csi.everest.io     # Dependent storage driver for the mounting.
       fsType: ext4
       volumeHandle: 459581af-e78c-4356-9e78-eaf9cd8525eb   # Volume ID of the EVS disk.
       volumeAttributes:
         everest.io/disk-mode: SCSI           # Device type of the EVS disk. Only SCSI is supported.
         everest.io/disk-volume-type: SAS     # EVS disk type.
         storage.kubernetes.io/csiProvisionerIdentity: everest-csi-provisioner
         everest.io/crypt-key-id: 0992dbda-6340-470e-a74e-4f0db288ed82    # (Optional) Encryption key ID. Mandatory for an encrypted disk.
     persistentVolumeReclaimPolicy: Delete    # Reclain policy.
     storageClassName: csi-disk               # Storage class name. The value must be csi-disk.

.. table:: **Table 2** Key parameters

   +-----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                                     | Description                                                                                                                                                                                                                                                                                 |
   +===============================================+=============================================================================================================================================================================================================================================================================================+
   | everest.io/reclaim-policy: retain-volume-only | This field is optional.                                                                                                                                                                                                                                                                     |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | Currently, only **retain-volume-only** is supported.                                                                                                                                                                                                                                        |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | This field is valid only when the everest version is 1.2.9 or later and the reclaim policy is Delete. If the reclaim policy is Delete and the current value is **retain-volume-only**, the associated PV is deleted while the underlying storage volume is retained, when a PVC is deleted. |
   +-----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | failure-domain.beta.kubernetes.io/region      | Region where the cluster is located.                                                                                                                                                                                                                                                        |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | For details about the value of **region**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.                                                                                                                                                        |
   +-----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | failure-domain.beta.kubernetes.io/zone        | AZ where the EVS volume is created. It must be the same as the AZ planned for the workload.                                                                                                                                                                                                 |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | For details about the value of **zone**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.                                                                                                                                                          |
   +-----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | volumeHandle                                  | Volume ID of the EVS disk.                                                                                                                                                                                                                                                                  |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | To obtain the volume ID, log in to the **Cloud Server Console**. In the navigation pane, choose **Elastic Volume Service** > **Disks**. Click the name of the target EVS disk to go to its details page. On the **Summary** tab page, click the copy button after **ID**.                   |
   +-----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | everest.io/disk-volume-type                   | EVS disk type. All letters are in uppercase.                                                                                                                                                                                                                                                |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | -  **SATA**: common I/O                                                                                                                                                                                                                                                                     |
   |                                               | -  **SAS**: high I/O                                                                                                                                                                                                                                                                        |
   |                                               | -  **SSD**: ultra-high I/O                                                                                                                                                                                                                                                                  |
   +-----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | everest.io/crypt-key-id                       | Encryption key ID. This field is mandatory when the volume is an encrypted volume.                                                                                                                                                                                                          |
   +-----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | persistentVolumeReclaimPolicy                 | A reclaim policy is supported when the cluster version is equal to or later than 1.19.10 and the everest version is equal to or later than 1.2.9.                                                                                                                                           |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | The Delete and Retain policies are supported.                                                                                                                                                                                                                                               |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | **Delete**:                                                                                                                                                                                                                                                                                 |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | -  If **everest.io/reclaim-policy** is not specified, both the PV and EVS disk are deleted when a PVC is deleted.                                                                                                                                                                           |
   |                                               | -  If **everest.io/reclaim-policy** is set to **retain-volume-only set**, when a PVC is deleted, the PV is deleted but the EVS resources are retained.                                                                                                                                      |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | **Retain**: When a PVC is deleted, the PV and underlying storage resources are not deleted. Instead, you must manually delete these resources. After that, the PV resource is in the Released state and cannot be bound to the PVC again.                                                   |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | If high data security is required, you are advised to select **Retain** to prevent data from being deleted by mistake.                                                                                                                                                                      |
   +-----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Creating an SFS Volume
----------------------

.. note::

   -  The SFS file system and the cluster must be in the same VPC.

**Using the CCE Console**

#. Log in to the CCE console.
#. Click the cluster name and access the cluster console. Choose **Storage** from the navigation pane, and click the **PersistentVolumes (PVs)** tab.
#. Click **Create Volume** in the upper right corner. In the dialog box displayed, set the volume parameters.

   -  **Volume Type**: Select **SFS**.
   -  Select SFS resources.
   -  **PV Name**: Enter a PV name.
   -  **Access Mode**: ReadWriteMany
   -  **Reclaim Policy**: Select **Delete** or **Retain** as required. For details, see :ref:`PV Reclaim Policy <cce_10_0379__section19999142414413>`.
   -  **Mount Options**: mount options. For details about the options, see :ref:`Setting Mount Options <cce_10_0337>`.

#. Click **Create**.

**Using YAML**

.. code-block::

   apiVersion: v1
   kind: PersistentVolume
   metadata:
     annotations:
       pv.kubernetes.io/provisioned-by: everest-csi-provisioner
       everest.io/reclaim-policy: retain-volume-only      # (Optional) The PV is deleted while the underlying volume is retained.
     name: cce-sfs-test
   spec:
     accessModes:
     - ReadWriteMany      # Access mode. The value must be ReadWriteMany for SFS.
     capacity:
       storage: 1Gi       # File storage capacity.
     csi:
       driver: disk.csi.everest.io   # Mount the dependent storage driver.
       fsType: nfs
       volumeHandle: 30b3d92a-0bc7-4610-b484-534660db81be   # SFS file system ID.
       volumeAttributes:
         everest.io/share-export-location:   # Shared path of the file storage
         storage.kubernetes.io/csiProvisionerIdentity: everest-csi-provisioner
     persistentVolumeReclaimPolicy: Retain    # Reclaim policy.
     storageClassName: csi-nas                # Storage class name
     mountOptions: []                         # Mount options

.. table:: **Table 3** Key parameters

   +-----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                                     | Description                                                                                                                                                                                                                                                                                 |
   +===============================================+=============================================================================================================================================================================================================================================================================================+
   | everest.io/reclaim-policy: retain-volume-only | This field is optional.                                                                                                                                                                                                                                                                     |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | Currently, only **retain-volume-only** is supported.                                                                                                                                                                                                                                        |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | This field is valid only when the everest version is 1.2.9 or later and the reclaim policy is Delete. If the reclaim policy is Delete and the current value is **retain-volume-only**, the associated PV is deleted while the underlying storage volume is retained, when a PVC is deleted. |
   +-----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | volumeHandle                                  | -  If SFS Capacity-Oriented file storage is used, enter the file storage ID.                                                                                                                                                                                                                |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               |    On the management console, choose **Service List** > **Storage** > **Scalable File Service**. In the SFS file system list, click the name of the target file system and copy the content following **ID** on the page displayed.                                                         |
   +-----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | everest.io/share-export-location              | Shared path of the file system.                                                                                                                                                                                                                                                             |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | On the management console, choose **Service List** > **Storage** > **Scalable File Service**. You can obtain the shared path of the file system from the **Mount Address** column.                                                                                                          |
   +-----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | mountOptions                                  | Mount options.                                                                                                                                                                                                                                                                              |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | If not specified, the following configurations are used by default. For details, see :ref:`SFS Volume Mount Options <cce_10_0337__section14888047833>`.                                                                                                                                     |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | .. code-block::                                                                                                                                                                                                                                                                             |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               |    mountOptions:                                                                                                                                                                                                                                                                            |
   |                                               |    - vers=3                                                                                                                                                                                                                                                                                 |
   |                                               |    - timeo=600                                                                                                                                                                                                                                                                              |
   |                                               |    - nolock                                                                                                                                                                                                                                                                                 |
   |                                               |    - hard                                                                                                                                                                                                                                                                                   |
   +-----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | everest.io/crypt-key-id                       | Encryption key ID. This field is mandatory when the volume is an encrypted volume.                                                                                                                                                                                                          |
   +-----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | persistentVolumeReclaimPolicy                 | A reclaim policy is supported when the cluster version is equal to or later than 1.19.10 and the everest version is equal to or later than 1.2.9.                                                                                                                                           |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | The options are as follows:                                                                                                                                                                                                                                                                 |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | **Delete**:                                                                                                                                                                                                                                                                                 |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | -  If **everest.io/reclaim-policy** is not specified, both the PV and SFS volume are deleted when a PVC is deleted.                                                                                                                                                                         |
   |                                               | -  If **everest.io/reclaim-policy** is set to **retain-volume-only set**, when a PVC is deleted, the PV is deleted but the file storage resources are retained.                                                                                                                             |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | **Retain**: When a PVC is deleted, the PV and underlying storage resources are not deleted. Instead, you must manually delete these resources. After that, the PV resource is in the Released state and cannot be bound to the PVC again.                                                   |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | If high data security is required, you are advised to select **Retain** to prevent data from being deleted by mistake.                                                                                                                                                                      |
   +-----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Creating an OBS Volume
----------------------

.. note::

   Secure containers do not support OBS volumes.

   A single user can create a maximum of 100 OBS buckets on the console. If you have a large number of CCE workloads and you want to mount an OBS bucket to every workload, you may easily run out of buckets. In this scenario, you are advised to use OBS through the OBS API or SDK and do not mount OBS buckets to the workload on the console.

**Using the CCE Console**

#. Log in to the CCE console.
#. Click the cluster name and access the cluster console. Choose **Storage** from the navigation pane, and click the **PersistentVolumes (PVs)** tab.
#. Click **Create Volume** in the upper right corner. In the dialog box displayed, set the volume parameters.

   -  **Volume Type**: Select **OBS**.
   -  Select OBS resources.
   -  **PV Name**: Enter a PV name.
   -  **Access Mode**: ReadWriteMany
   -  **Reclaim Policy**: Select **Delete** or **Retain** as required. For details, see :ref:`PV Reclaim Policy <cce_10_0379__section19999142414413>`.
   -  **Secret**: You can customize the access key (AK/SK) for mounting an OBS volume. You can use the AK/SK to create a secret and mount the secret to the PV. For details, see :ref:`Using a Custom AK/SK to Mount an OBS Volume <cce_10_0336>`.
   -  **Mount Options**: mount options. For details about the options, see :ref:`Setting Mount Options <cce_10_0337>`.

#. Click **Create**.

**Using YAML**

.. code-block::

   apiVersion: v1
   kind: PersistentVolume
   metadata:
     annotations:
       pv.kubernetes.io/provisioned-by: everest-csi-provisioner
       everest.io/reclaim-policy: retain-volume-only         # (Optional) The PV is deleted while the underlying volume is retained.
     name: cce-obs-test
   spec:
     accessModes:
     - ReadWriteMany                      # Access mode. The value must be ReadWriteMany for OBS.
     capacity:
       storage: 1Gi      # Storage capacity. This parameter is set only to meet the PV format requirements. It can be set to any value. The actual OBS space size is not limited by this value.
     csi:
       driver: obs.csi.everest.io        # Dependent storage driver for the mounting.
       fsType: obsfs                      # OBS file type.
       volumeHandle: cce-obs-bucket       # OBS bucket name.
       volumeAttributes:
         everest.io/obs-volume-type: STANDARD
         everest.io/region: eu-de

         storage.kubernetes.io/csiProvisionerIdentity: everest-csi-provisioner
       nodePublishSecretRef:
         name: test-user
         namespace: default
     persistentVolumeReclaimPolicy: Retain       # Reclaim policy.
     storageClassName: csi-obs                   # Storage class name. The value must be csi-obs for OBS.
     mountOptions: []                            # Mount options.

.. table:: **Table 4** Key parameters

   +-----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                                     | Description                                                                                                                                                                                                                                                                                 |
   +===============================================+=============================================================================================================================================================================================================================================================================================+
   | everest.io/reclaim-policy: retain-volume-only | This field is optional.                                                                                                                                                                                                                                                                     |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | Currently, only **retain-volume-only** is supported.                                                                                                                                                                                                                                        |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | This field is valid only when the everest version is 1.2.9 or later and the reclaim policy is Delete. If the reclaim policy is Delete and the current value is **retain-volume-only**, the associated PV is deleted while the underlying storage volume is retained, when a PVC is deleted. |
   +-----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | fsType                                        | File type. The value can be **obsfs** or **s3fs**. If the value is **s3fs**, an OBS bucket is created and mounted using s3fs. If the value is **obsfs**, an OBS parallel file system is created and mounted using obsfs. You are advised to set this field to **obsfs**.                    |
   +-----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | volumeHandle                                  | OBS bucket name.                                                                                                                                                                                                                                                                            |
   +-----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | everest.io/obs-volume-type                    | Storage class, including **STANDARD** (standard bucket) and **WARM** (infrequent access bucket).                                                                                                                                                                                            |
   +-----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | everest.io/region                             | Region where the OBS bucket is deployed.                                                                                                                                                                                                                                                    |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | For details about the value of **region**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.                                                                                                                                                        |
   +-----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | nodePublishSecretRef                          | Access key (AK/SK) used for mounting the object storage volume. You can use the AK/SK to create a secret and mount it to the PV. For details, see :ref:`Using a Custom AK/SK to Mount an OBS Volume <cce_10_0336>`.                                                                         |
   +-----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | mountOptions                                  | Mount options. For details, see :ref:`OBS Volume Mount Options <cce_10_0337__section1254912109811>`.                                                                                                                                                                                        |
   +-----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | persistentVolumeReclaimPolicy                 | A reclaim policy is supported when the cluster version is equal to or later than 1.19.10 and the everest version is equal to or later than 1.2.9.                                                                                                                                           |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | The Delete and Retain policies are supported.                                                                                                                                                                                                                                               |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | **Delete**:                                                                                                                                                                                                                                                                                 |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | -  If **everest.io/reclaim-policy** is not specified, both the PV and OBS volume are deleted when a PVC is deleted.                                                                                                                                                                         |
   |                                               | -  If **everest.io/reclaim-policy** is set to **retain-volume-only set**, when a PVC is deleted, the PV is deleted but the object storage resources are retained.                                                                                                                           |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | **Retain**: When a PVC is deleted, the PV and underlying storage resources are not deleted. Instead, you must manually delete these resources. After that, the PV resource is in the Released state and cannot be bound to the PVC again.                                                   |
   |                                               |                                                                                                                                                                                                                                                                                             |
   |                                               | If high data security is required, you are advised to select **Retain** to prevent data from being deleted by mistake.                                                                                                                                                                      |
   +-----------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Creating an SFS Turbo Volume
----------------------------

.. note::

   SFS Turbo and the cluster must be in the same VPC.

**Using the CCE Console**

#. Log in to the CCE console.
#. Click the cluster name and access the cluster console. Choose **Storage** from the navigation pane, and click the **PersistentVolumes (PVs)** tab.
#. Click **Create Volume** in the upper right corner. In the dialog box displayed, set the volume parameters.

   -  **Volume Type**: Select **SFS Turbo**.
   -  **SFS Turbo**: Select SFS Turbo resources.
   -  **PV Name**: Enter a PV name.
   -  **Access Mode**: ReadWriteMany
   -  **Reclaim Policy**: Select **Retain**. For details, see :ref:`PV Reclaim Policy <cce_10_0379__section19999142414413>`.
   -  **Mount Options**: mount options. For details about the options, see :ref:`Setting Mount Options <cce_10_0337>`.

#. Click **Create**.

**Using YAML**

.. code-block::

   apiVersion: v1
   kind: PersistentVolume
   metadata:
     annotations:
       pv.kubernetes.io/provisioned-by: everest-csi-provisioner
     name: cce-sfsturbo-test
   spec:
     accessModes:
       - ReadWriteMany       # Access mode. The value must be ReadWriteMany for SFS Turbo.
     capacity:
       storage: 100.00Gi     # SFS Turbo volume capacity.
     csi:
       driver: sfsturbo.csi.everest.io    # Dependent storage driver for the mounting.
       fsType: nfs
       volumeHandle: 6674bd0a-d760-49de-bb9e-805c7883f047      # SFS Turbo volume ID.
       volumeAttributes:
         everest.io/share-export-location: 192.168.0.85:/      # Shared path of the SFS Turbo volume.
         storage.kubernetes.io/csiProvisionerIdentity: everest-csi-provisioner
     persistentVolumeReclaimPolicy: Retain     # Reclaim policy.
     storageClassName: csi-sfsturbo            # Storage class name. The value must be csi-sfsturbo for SFS Turbo.
     mountOptions: []                          # Mount options.

.. table:: **Table 5** Key parameters

   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                         | Description                                                                                                                                                                                                                               |
   +===================================+===========================================================================================================================================================================================================================================+
   | volumeHandle                      | SFS Turbo volume ID.                                                                                                                                                                                                                      |
   |                                   |                                                                                                                                                                                                                                           |
   |                                   | You can obtain the ID on the SFS Turbo storage instance details page on the SFS console.                                                                                                                                                  |
   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | everest.io/share-export-location  | Shared path of the SFS Turbo volume.                                                                                                                                                                                                      |
   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | mountOptions                      | Mount options.                                                                                                                                                                                                                            |
   |                                   |                                                                                                                                                                                                                                           |
   |                                   | If not specified, the following configurations are used by default. For details, see :ref:`SFS Volume Mount Options <cce_10_0337__section14888047833>`.                                                                                   |
   |                                   |                                                                                                                                                                                                                                           |
   |                                   | .. code-block::                                                                                                                                                                                                                           |
   |                                   |                                                                                                                                                                                                                                           |
   |                                   |    mountOptions:                                                                                                                                                                                                                          |
   |                                   |    - vers=3                                                                                                                                                                                                                               |
   |                                   |    - timeo=600                                                                                                                                                                                                                            |
   |                                   |    - nolock                                                                                                                                                                                                                               |
   |                                   |    - hard                                                                                                                                                                                                                                 |
   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | persistentVolumeReclaimPolicy     | A reclaim policy is supported when the cluster version is equal to or later than 1.19.10 and the everest version is equal to or later than 1.2.9.                                                                                         |
   |                                   |                                                                                                                                                                                                                                           |
   |                                   | The Delete and Retain policies are supported.                                                                                                                                                                                             |
   |                                   |                                                                                                                                                                                                                                           |
   |                                   | **Delete**:                                                                                                                                                                                                                               |
   |                                   |                                                                                                                                                                                                                                           |
   |                                   | -  If **everest.io/reclaim-policy** is not specified, both the PV and SFS Turbo volume are deleted when a PVC is deleted.                                                                                                                 |
   |                                   | -  If **everest.io/reclaim-policy** is set to **retain-volume-only set**, when a PVC is deleted, the PV is deleted but the SFF Turbo resources are retained.                                                                              |
   |                                   |                                                                                                                                                                                                                                           |
   |                                   | **Retain**: When a PVC is deleted, the PV and underlying storage resources are not deleted. Instead, you must manually delete these resources. After that, the PV resource is in the Released state and cannot be bound to the PVC again. |
   |                                   |                                                                                                                                                                                                                                           |
   |                                   | If high data security is required, you are advised to select **Retain** to prevent data from being deleted by mistake.                                                                                                                    |
   +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
