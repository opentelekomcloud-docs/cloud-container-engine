:original_name: cce_10_0614.html

.. _cce_10_0614:

Using an Existing EVS Disk Through a Static PV
==============================================

CCE allows you to create a PV using an existing EVS disk. Then, you can create a PVC and bind it to the PV. This method is suitable for scenarios where underlying storage is available.

Prerequisites
-------------

-  You have created a cluster and installed the :ref:`CCE Container Storage (Everest) <cce_10_0066>` add-on in the cluster.
-  You have created an EVS disk that meets the following requirements:

   -  The EVS disk cannot be a system disk or shared disk.
   -  The EVS disk must be of the **SCSI** type (the default disk type is **VBD** when you create an EVS disk).
   -  The EVS disk must be available and not used by other resources.
   -  The AZ of the EVS disk must be the same as that of the cluster node. Otherwise, the EVS disk cannot be mounted and the pod cannot start.
   -  If the EVS disk is encrypted, the key must be available.
   -  EVS disks that have been partitioned are not supported.

-  To create a cluster using commands, ensure kubectl is used. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

Notes and Constraints
---------------------

-  EVS disks cannot be attached across AZs and cannot be used by multiple workloads, multiple pods of the same workload, or multiple tasks. Data sharing of a shared disk is not supported between nodes in a CCE cluster. If an EVS disk is attached to multiple nodes, I/O conflicts and data cache conflicts may occur. Therefore, select only one pod when creating a Deployment that uses EVS disks.

-  For clusters earlier than v1.19.10, if an HPA policy is used to scale out a workload with EVS volumes mounted, the existing pods cannot be read or written when a new pod is scheduled to another node.

   For clusters of v1.19.10 and later, if an HPA policy is used to scale out a workload with EVS volumes mounted, a new pod cannot be started because EVS disks cannot be attached.

Using an Existing EVS Disk on the Console
-----------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Statically create a PVC and PV.

   a. Choose **Storage** in the navigation pane. In the right pane, click the **PVCs** tab. Click **Create PVC** in the upper right corner. In the dialog box displayed, configure PVC parameters.

      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                   |
      +===================================+===============================================================================================================================================================================================================+
      | PVC Type                          | In this example, select **EVS**.                                                                                                                                                                              |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PVC Name                          | Enter the PVC name, which must be unique in a namespace.                                                                                                                                                      |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Creation Method                   | -  If underlying storage is available, create a PV or use an existing PV to statically create a PVC.                                                                                                          |
      |                                   | -  If no underlying storage is available, select **Dynamically provision**. For details, see :ref:`Using an EVS Disk Through a Dynamic PV <cce_10_0615>`.                                                     |
      |                                   |                                                                                                                                                                                                               |
      |                                   | In this example, select **Create new** to create both a PV and PVC on the console.                                                                                                                            |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PV\ :sup:`a`                      | Select an existing PV in the cluster. For details about how to create a PV, see "Creating a storage volume" in :ref:`Related Operations <cce_10_0614__section16505832153318>`.                                |
      |                                   |                                                                                                                                                                                                               |
      |                                   | You do not need to specify this parameter in this example.                                                                                                                                                    |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | EVS\ :sup:`b`                     | Click **Select EVS**. On the displayed page, select the EVS volume that meets your requirements and click **OK**.                                                                                             |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PV Name\ :sup:`b`                 | Enter the PV name, which must be unique in the same cluster.                                                                                                                                                  |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Access Mode\ :sup:`b`             | EVS volumes support only **ReadWriteOnce**, indicating that a storage volume can be mounted to one node in read/write mode. For details, see :ref:`Volume Access Modes <cce_10_0378__section43881411172418>`. |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Reclaim Policy\ :sup:`b`          | You can select **Delete** or **Retain** to specify the reclaim policy of the underlying storage when the PVC is deleted. For details, see :ref:`PV Reclaim Policy <cce_10_0378__section19999142414413>`.      |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

      .. note::

         a: The parameter is available when **Creation Method** is set to **Use existing**.

         b: The parameter is available when **Creation Method** is set to **Create new**.

   b. Click **Create** to create a PVC and a PV.

      You can choose **Storage** in the navigation pane and view the created PVC and PV on the **PVCs** and **PVs** tab pages, respectively.

#. Create an application.

   a. Choose **Workloads** in the navigation pane. In the right pane, click the **StatefulSets** tab.

   b. Click **Create Workload** in the upper right corner. On the displayed page, click **Data Storage** in the **Container Information** area under **Container Settings** and choose **Add Volume** > **PVC**.

      Mount and use storage volumes, as shown in :ref:`Table 1 <cce_10_0614__table2529244345>`. For details about other parameters, see :ref:`Workloads <cce_10_0046>`.

      .. _cce_10_0614__table2529244345:

      .. table:: **Table 1** Mounting a storage volume

         +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                        |
         +===================================+====================================================================================================================================================================================================================================================================================================================================================================================================================================================+
         | PVC                               | Select an existing EVS volume.                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
         |                                   | An EVS volume can be mounted to only one workload.                                                                                                                                                                                                                                                                                                                                                                                                 |
         +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Mount Path                        | Enter a mount path, for example, **/tmp**.                                                                                                                                                                                                                                                                                                                                                                                                         |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
         |                                   | This parameter specifies a container path to which a data volume will be mounted. Do not mount the volume to a system directory such as **/** or **/var/run**. This may lead to container errors. Mount the volume to an empty directory. If the directory is not empty, ensure that there are no files that affect container startup. Otherwise, the files will be replaced, leading to container startup failures or workload creation failures. |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
         |                                   | .. important::                                                                                                                                                                                                                                                                                                                                                                                                                                     |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
         |                                   |    NOTICE:                                                                                                                                                                                                                                                                                                                                                                                                                                         |
         |                                   |    If a volume is mounted to a high-risk directory, use an account with minimum permissions to start the container. Otherwise, high-risk files on the host may be damaged.                                                                                                                                                                                                                                                                         |
         +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Subpath                           | Enter the subpath of the storage volume and mount a path in the storage volume to the container. In this way, different folders of the same storage volume can be used in a single pod. **tmp**, for example, indicates that data in the mount path of the container is stored in the **tmp** folder of the storage volume. If this parameter is left blank, the root path will be used by default.                                                |
         +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Permission                        | -  **Read-only**: You can only read the data in the mounted volumes.                                                                                                                                                                                                                                                                                                                                                                               |
         |                                   | -  **Read-write**: You can modify the data volumes mounted to the path. Newly written data will not be migrated if the container is migrated, which may cause data loss.                                                                                                                                                                                                                                                                           |
         +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

      In this example, the disk is mounted to the **/data** path of the container. The container data generated in this path is stored in the EVS disk.

      .. note::

         A non-shared EVS disk can be attached to only one workload pod. If there are multiple pods, extra pods cannot start properly. Ensure that the number of workload pods is 1 if an EVS disk is attached.

   c. After the configuration, click **Create Workload**.

      After the workload is created, the data in the container mount directory will be persistently stored. Verify the storage by referring to :ref:`Verifying Data Persistence <cce_10_0614__section11593165910013>`.

Using an Existing EVS Disk Through kubectl
------------------------------------------

#. Use kubectl to access the cluster.
#. Create a PV. If a PV has been created in your cluster, skip this step. When using a YAML file to create a PV, do not use parameters that are not declared in the file, such as **status**, **spec.claimRef**, and **annotation.everest.io/set-disk-metadata**. Otherwise, the PV may be abnormal.

   a. .. _cce_10_0614__li162841212145314:

      Create the **pv-evs.yaml** file.

      .. code-block::

         apiVersion: v1
         kind: PersistentVolume
         metadata:
           annotations:
             pv.kubernetes.io/provisioned-by: everest-csi-provisioner
             everest.io/reclaim-policy: retain-volume-only         # (Optional) The underlying volume is retained when the PV is deleted.
           name: pv-evs    # PV name
           labels:
             failure-domain.beta.kubernetes.io/region: <your_region>   # Region of the node where the application is to be deployed
             failure-domain.beta.kubernetes.io/zone: <your_zone>       # AZ of the node where the application is to be deployed
         spec:
           accessModes:
             - ReadWriteOnce     # Access mode, which must be ReadWriteOnce for EVS disks
           capacity:
             storage: 10Gi       # EVS disk capacity, in the unit of GiB. The value ranges from 1 to 32768.
           csi:
             driver: disk.csi.everest.io     # Dependent storage driver for the mounting
             fsType: ext4    # Must be the same as that of the original file system of the disk.
             volumeHandle: <your_volume_id>   # EVS volume ID
             volumeAttributes:
               everest.io/disk-mode: SCSI           # Device type of the EVS disk. Only SCSI is supported.
               everest.io/disk-volume-type: SAS     # EVS disk type
               storage.kubernetes.io/csiProvisionerIdentity: everest-csi-provisioner
               everest.io/crypt-key-id: <your_key_id>    # (Optional) Encryption key ID. Mandatory for an encrypted disk.
           persistentVolumeReclaimPolicy: Delete    # Reclaim policy
           storageClassName: csi-disk              # StorageClass name. The value must be csi-disk for EVS disks.

      .. table:: **Table 2** Key parameters

         +-----------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                                     | Mandatory             | Description                                                                                                                                                                                                                                                                                                              |
         +===============================================+=======================+==========================================================================================================================================================================================================================================================================================================================+
         | everest.io/reclaim-policy: retain-volume-only | No                    | Optional.                                                                                                                                                                                                                                                                                                                |
         |                                               |                       |                                                                                                                                                                                                                                                                                                                          |
         |                                               |                       | Only **retain-volume-only** is supported.                                                                                                                                                                                                                                                                                |
         |                                               |                       |                                                                                                                                                                                                                                                                                                                          |
         |                                               |                       | This parameter is valid only when the Everest version is 1.2.9 or later and the reclaim policy is **Delete**. If the reclaim policy is **Delete** and the current value is **retain-volume-only**, the associated PV is deleted while the underlying storage volume is retained, when a PVC is deleted.                  |
         +-----------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | failure-domain.beta.kubernetes.io/region      | Yes                   | Region where the cluster is located.                                                                                                                                                                                                                                                                                     |
         |                                               |                       |                                                                                                                                                                                                                                                                                                                          |
         |                                               |                       | For details about its value, see `Regions and Endpoints <https://docs.otc.t-systems.com/regions-and-endpoints/index.html>`__.                                                                                                                                                                                            |
         +-----------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | failure-domain.beta.kubernetes.io/zone        | Yes                   | AZ where the EVS volume is created. It must be the same as the AZ planned for the workload.                                                                                                                                                                                                                              |
         |                                               |                       |                                                                                                                                                                                                                                                                                                                          |
         |                                               |                       | For details about its value, see `Regions and Endpoints <https://docs.otc.t-systems.com/regions-and-endpoints/index.html>`__.                                                                                                                                                                                            |
         +-----------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | fsType                                        | Yes                   | File system type, which defaults to **ext4**.                                                                                                                                                                                                                                                                            |
         |                                               |                       |                                                                                                                                                                                                                                                                                                                          |
         |                                               |                       | The value can be **ext4** or **xfs**. The restrictions on using **xfs** are as follows:                                                                                                                                                                                                                                  |
         |                                               |                       |                                                                                                                                                                                                                                                                                                                          |
         |                                               |                       | -  The nodes must run CentOS 7, HCE OS 2.0, or Ubuntu 22.04, and the Everest version in the cluster must be 2.3.2 or later.                                                                                                                                                                                              |
         |                                               |                       | -  Only common containers are supported.                                                                                                                                                                                                                                                                                 |
         +-----------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | volumeHandle                                  | Yes                   | Volume ID of the EVS disk.                                                                                                                                                                                                                                                                                               |
         |                                               |                       |                                                                                                                                                                                                                                                                                                                          |
         |                                               |                       | To obtain a volume ID, log in to the **Cloud Server Console**. In the navigation pane, choose **Elastic Volume Service** > **Disks**. Click the name of the target EVS disk to go to its details page. On the **Summary** tab page, click the copy button after **ID**.                                                  |
         +-----------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | everest.io/disk-volume-type                   | Yes                   | EVS disk type. All letters are in uppercase.                                                                                                                                                                                                                                                                             |
         |                                               |                       |                                                                                                                                                                                                                                                                                                                          |
         |                                               |                       | -  **SATA**: common I/O                                                                                                                                                                                                                                                                                                  |
         |                                               |                       | -  **SAS**: high I/O                                                                                                                                                                                                                                                                                                     |
         |                                               |                       | -  **SSD**: ultra-high I/O                                                                                                                                                                                                                                                                                               |
         |                                               |                       | -  **GPSSD**: general-purpose SSD                                                                                                                                                                                                                                                                                        |
         |                                               |                       | -  **ESSD**: extreme SSD                                                                                                                                                                                                                                                                                                 |
         +-----------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | everest.io/crypt-key-id                       | No                    | Mandatory when the EVS disk is encrypted. Enter the encryption key ID selected during EVS disk creation.                                                                                                                                                                                                                 |
         |                                               |                       |                                                                                                                                                                                                                                                                                                                          |
         |                                               |                       | To obtain an encryption key ID, log in to the **Cloud Server Console**. In the navigation pane, choose **Elastic Volume Service** > **Disks**. Click the name of the target EVS disk to go to its details page. On the **Summary** tab page, copy the value of **KMS Key ID** in the **Configuration Information** area. |
         +-----------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | persistentVolumeReclaimPolicy                 | Yes                   | A reclaim policy is supported when the cluster version is or later than 1.19.10 and the Everest version is or later than 1.2.9.                                                                                                                                                                                          |
         |                                               |                       |                                                                                                                                                                                                                                                                                                                          |
         |                                               |                       | The **Delete** and **Retain** reclaim policies are supported. For details, see :ref:`PV Reclaim Policy <cce_10_0378__section19999142414413>`. If high data security is required, select **Retain** to prevent data from being deleted by mistake.                                                                        |
         |                                               |                       |                                                                                                                                                                                                                                                                                                                          |
         |                                               |                       | **Delete**:                                                                                                                                                                                                                                                                                                              |
         |                                               |                       |                                                                                                                                                                                                                                                                                                                          |
         |                                               |                       | -  If **everest.io/reclaim-policy** is not specified, both the PV and EVS disk will be deleted when a PVC is deleted.                                                                                                                                                                                                    |
         |                                               |                       | -  If **everest.io/reclaim-policy** is set to **retain-volume-only**, when a PVC is deleted, the PV will be deleted but the EVS disk will be retained.                                                                                                                                                                   |
         |                                               |                       |                                                                                                                                                                                                                                                                                                                          |
         |                                               |                       | **Retain**: When a PVC is deleted, both the PV and underlying storage resources will be retained. You need to manually delete these resources. After the PVC is deleted, the PV is in the **Released** state and cannot be bound to a PVC again.                                                                         |
         +-----------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storageClassName                              | Yes                   | StorageClass name, which is **csi-disk** for an EVS disk.                                                                                                                                                                                                                                                                |
         +-----------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   b. Run the following command to create a PV:

      .. code-block::

         kubectl apply -f pv-evs.yaml

#. Create a PVC.

   a. Create the **pvc-evs.yaml** file.

      .. code-block::

         apiVersion: v1
         kind: PersistentVolumeClaim
         metadata:
           name: pvc-evs
           namespace: default
           annotations:
               everest.io/disk-volume-type: SAS    # EVS disk type
             everest.io/crypt-key-id: <your_key_id>    # (Optional) Encryption key ID. Mandatory for an encrypted disk.

           labels:
             failure-domain.beta.kubernetes.io/region: <your_region>   # Region of the node where the application is to be deployed
             failure-domain.beta.kubernetes.io/zone: <your_zone>       # AZ of the node where the application is to be deployed
         spec:
           accessModes:
           - ReadWriteOnce               # The value must be ReadWriteOnce for EVS disks.
           resources:
             requests:
               storage: 10Gi             # EVS disk capacity, ranging from 1 to 32768. The value must be the same as the storage size of the existing PV.
           storageClassName: csi-disk    # The StorageClass is EVS.
           volumeName: pv-evs            # PV name

      .. table:: **Table 3** Key parameters

         +------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                                | Mandatory             | Description                                                                                                                   |
         +==========================================+=======================+===============================================================================================================================+
         | failure-domain.beta.kubernetes.io/region | Yes                   | Region where the cluster is located.                                                                                          |
         |                                          |                       |                                                                                                                               |
         |                                          |                       | For details about its value, see `Regions and Endpoints <https://docs.otc.t-systems.com/regions-and-endpoints/index.html>`__. |
         +------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------+
         | failure-domain.beta.kubernetes.io/zone   | Yes                   | AZ where the EVS volume is created. It must be the same as the AZ planned for the workload.                                   |
         |                                          |                       |                                                                                                                               |
         |                                          |                       | For details about its value, see `Regions and Endpoints <https://docs.otc.t-systems.com/regions-and-endpoints/index.html>`__. |
         +------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------+
         | storage                                  | Yes                   | Requested capacity in the PVC, in Gi.                                                                                         |
         |                                          |                       |                                                                                                                               |
         |                                          |                       | The value must be the same as the storage size of the existing PV.                                                            |
         +------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------+
         | volumeName                               | Yes                   | PV name, which must be the same as the PV name in :ref:`1 <cce_10_0614__li162841212145314>`.                                  |
         +------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------+
         | storageClassName                         | Yes                   | StorageClass name, which must be the same as the StorageClass of the PV in :ref:`1 <cce_10_0614__li162841212145314>`.         |
         |                                          |                       |                                                                                                                               |
         |                                          |                       | The StorageClass for EVS volumes is **csi-disk**.                                                                             |
         +------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------+

   b. Run the following command to create a PVC:

      .. code-block::

         kubectl apply -f pvc-evs.yaml

#. Create an application.

   a. Create a file named **web-evs.yaml**. In this example, the EVS volume is mounted to the **/data** path.

      .. code-block::

         apiVersion: apps/v1
         kind: StatefulSet
         metadata:
           name: web-evs
           namespace: default
         spec:
          replicas: 1            # The number of workload replicas that use the EVS volume must be 1.
           selector:
             matchLabels:
               app: web-evs
           serviceName: web-evs   # Headless Service name
           template:
             metadata:
               labels:
                 app: web-evs
             spec:
               containers:
               - name: container-1
                 image: nginx:latest
                 volumeMounts:
                 - name: pvc-disk    # Volume name, which must be the same as the volume name in the volumes field
                   mountPath: /data  # Location where the storage volume is mounted
               imagePullSecrets:
                 - name: default-secret
               volumes:
                 - name: pvc-disk    # Volume name, which can be customized
                   persistentVolumeClaim:
                     claimName: pvc-evs    # Name of the created PVC
         ---
         apiVersion: v1
         kind: Service
         metadata:
           name: web-evs   # Headless Service name
           namespace: default
           labels:
             app: web-evs
         spec:
           selector:
             app: web-evs
           clusterIP: None
           ports:
             - name: web-evs
               targetPort: 80
               nodePort: 0
               port: 80
               protocol: TCP
           type: ClusterIP

   b. Run the following command to create a workload to which the EVS volume is mounted:

      .. code-block::

         kubectl apply -f web-evs.yaml

      After the workload is created, the data in the container mount directory will be persistently stored. Verify the storage by referring to :ref:`Verifying Data Persistence <cce_10_0614__section11593165910013>`.

.. _cce_10_0614__section11593165910013:

Verifying Data Persistence
--------------------------

#. View the deployed application and EVS volume files.

   a. Run the following command to view the created pod:

      .. code-block::

         kubectl get pod | grep web-evs

      Expected output:

      .. code-block::

         web-evs-0                  1/1     Running   0               38s

   b. Run the following command to check whether the EVS volume has been mounted to the **/data** path:

      .. code-block::

         kubectl exec web-evs-0 -- df | grep data

      Expected output:

      .. code-block::

         /dev/sdc              10255636     36888  10202364   0% /data

   c. Run the following command to check the files in the **/data** path:

      .. code-block::

         kubectl exec web-evs-0 -- ls /data

      Expected output:

      .. code-block::

         lost+found

#. Run the following command to create a file named **static** in the **/data** path:

   .. code-block::

      kubectl exec web-evs-0 --  touch /data/static

#. Run the following command to check the files in the **/data** path:

   .. code-block::

      kubectl exec web-evs-0 -- ls /data

   Expected output:

   .. code-block::

      lost+found
      static

#. Run the following command to delete the pod named **web-evs-0**:

   .. code-block::

      kubectl delete pod web-evs-0

   Expected output:

   .. code-block::

      pod "web-evs-0" deleted

#. After the deletion, the StatefulSet controller automatically creates a replica with the same name. Run the following command to check whether the files in the **/data** path have been modified:

   .. code-block::

      kubectl exec web-evs-0 -- ls /data

   Expected output:

   .. code-block::

      lost+found
      static

   The **static** file is retained, indicating that the data in the EVS volume can be stored persistently.

.. _cce_10_0614__section16505832153318:

Related Operations
------------------

You can also perform the operations listed in :ref:`Table 4 <cce_10_0614__table1619535674020>`.

.. _cce_10_0614__table1619535674020:

.. table:: **Table 4** Related operations

   +---------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Operation                             | Description                                                                                                                                | Procedure                                                                                                                                                                                                                            |
   +=======================================+============================================================================================================================================+======================================================================================================================================================================================================================================+
   | Creating a storage volume (PV)        | Create a PV on the CCE console.                                                                                                            | #. Choose **Storage** in the navigation pane. In the right pane, click the **PVs** tab. Click **Create PersistentVolume** in the upper right corner. In the dialog box displayed, configure parameters.                              |
   |                                       |                                                                                                                                            |                                                                                                                                                                                                                                      |
   |                                       |                                                                                                                                            |    -  **Volume Type**: Select **EVS**.                                                                                                                                                                                               |
   |                                       |                                                                                                                                            |    -  **EVS**: Click **Select EVS**. On the displayed page, select the EVS volume that meets your requirements and click **OK**.                                                                                                     |
   |                                       |                                                                                                                                            |    -  **PV Name**: Enter the PV name, which must be unique in a cluster.                                                                                                                                                             |
   |                                       |                                                                                                                                            |    -  **Access Mode**: EVS volumes support only **ReadWriteOnce**, indicating that a storage volume can be mounted to one node in read/write mode. For details, see :ref:`Volume Access Modes <cce_10_0378__section43881411172418>`. |
   |                                       |                                                                                                                                            |    -  **Reclaim Policy**: **Delete** or **Retain** is supported. For details, see :ref:`PV Reclaim Policy <cce_10_0378__section19999142414413>`.                                                                                     |
   |                                       |                                                                                                                                            |                                                                                                                                                                                                                                      |
   |                                       |                                                                                                                                            | #. Click **Create**.                                                                                                                                                                                                                 |
   +---------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Expanding the capacity of an EVS disk | Quickly expand the capacity of an attached EVS disk on the CCE console.                                                                    | #. Choose **Storage** in the navigation pane. In the right pane, click the **PVCs** tab. Click **More** in the **Operation** column of the target PVC and select **Scale-out**.                                                      |
   |                                       |                                                                                                                                            | #. Enter the capacity to be added and click **OK**.                                                                                                                                                                                  |
   +---------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Viewing events                        | View event names, event types, number of occurrences, Kubernetes events, first occurrence time, and last occurrence time of the PVC or PV. | #. Choose **Storage** in the navigation pane. In the right pane, click the **PVCs** or **PVs** tab.                                                                                                                                  |
   |                                       |                                                                                                                                            | #. Click **View Events** in the **Operation** column of the target PVC or PV to view events generated within one hour (events are retained for one hour).                                                                            |
   +---------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Viewing a YAML file                   | View, copy, or download the YAML file of a PVC or PV.                                                                                      | #. Choose **Storage** in the navigation pane. In the right pane, click the **PVCs** or **PVs** tab.                                                                                                                                  |
   |                                       |                                                                                                                                            | #. Click **View YAML** in the **Operation** column of the target PVC or PV to view or download the YAML.                                                                                                                             |
   +---------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
