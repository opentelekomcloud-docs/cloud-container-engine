:original_name: cce_10_0614.html

.. _cce_10_0614:

Using an Existing EVS Disk Through a Static PV
==============================================

CCE allows you to create a PV using an existing EVS disk. After the PV is created, you can create a PVC and bind it to the PV. This mode applies to scenarios where the underlying storage is available.

Prerequisites
-------------

-  You have created a cluster and installed the CSI add-on (:ref:`everest <cce_10_0066>`) in the cluster.
-  You have created an EVS disk that meets the following requirements:

   -  The existing EVS disk cannot be a system disk, DSS disk, or shared disk.
   -  The device type of the EVS disk must be **SCSI** (the default device type is **VBD** when you purchase an EVS disk).
   -  The EVS disk must be available and not used by other resources.
   -  The AZ of the EVS disk must be the same as that of the cluster node. Otherwise, the EVS disk cannot be mounted and the pod cannot start.
   -  If the EVS disk is encrypted, the key must be available.
   -  EVS disks that have partitions or use non-ext4 file systems are not supported.

-  If you want to create a cluster using commands, use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

Constraints
-----------

-  EVS disks cannot be attached across AZs and cannot be used by multiple workloads, multiple pods of the same workload, or multiple tasks. Data sharing of a shared disk is not supported between nodes in a CCE cluster. If an EVS disk is attacked to multiple nodes, I/O conflicts and data cache conflicts may occur. Therefore, create only one pod when creating a Deployment that uses EVS disks.

-  For clusters earlier than v1.19.10, if an HPA policy is used to scale out a workload with EVS disks attached, the existing pods cannot be read or written when a new pod is scheduled to another node.

   For clusters of v1.19.10 and later, if an HPA policy is used to scale out a workload with EVS disks attached, a new pod cannot be started because EVS disks cannot be attached.

Using an Existing EVS Disk on the Console
-----------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Statically create a PVC and PV.

   a. Choose **Storage** from the navigation pane, and click the **PersistentVolumeClaims (PVCs)** tab. Click **Create PVC** in the upper right corner. In the dialog box displayed, configure the PVC parameters.

      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                 |
      +===================================+=============================================================================================================================================================================================================+
      | PVC Type                          | In this example, select **EVS**.                                                                                                                                                                            |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PVC Name                          | Enter the PVC name, which must be unique in the same namespace.                                                                                                                                             |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Creation Method                   | -  If underlying storage is available, create a storage volume or use an existing storage volume to statically create a PVC based on whether a PV has been created.                                         |
      |                                   | -  If no underlying storage is available, select **Dynamically provision**. For details, see :ref:`Using an EVS Disk Through a Dynamic PV <cce_10_0615>`.                                                   |
      |                                   |                                                                                                                                                                                                             |
      |                                   | In this example, select **Create new** to create a PV and PVC at the same time on the console.                                                                                                              |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PV\ :sup:`a`                      | Select an existing PV in the cluster. Create a PV in advance. For details, see "Creating a storage volume" in :ref:`Related Operations <cce_10_0614__section16505832153318>`.                               |
      |                                   |                                                                                                                                                                                                             |
      |                                   | In this example, you do not need to set this parameter.                                                                                                                                                     |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | EVS\ :sup:`b`                     | Click **Select EVS**. On the displayed page, select the EVS disk that meets your requirements and click **OK**.                                                                                             |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PV Name\ :sup:`b`                 | Enter the PV name, which must be unique in the same cluster.                                                                                                                                                |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Access Mode\ :sup:`b`             | EVS disks support only **ReadWriteOnce**, indicating that a storage volume can be mounted to one node in read/write mode. For details, see :ref:`Volume Access Modes <cce_10_0378__section43881411172418>`. |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Reclaim Policy\ :sup:`b`          | You can select **Delete** or **Retain** to specify the reclaim policy of the underlying storage when the PVC is deleted. For details, see :ref:`PV Reclaim Policy <cce_10_0378__section19999142414413>`.    |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

      .. note::

         a: The parameter is available when **Creation Method** is set to **Use existing**.

         b: The parameter is available when **Creation Method** is set to **Create new**.

   b. Click **Create** to create a PVC and a PV.

      You can choose **Storage** in the navigation pane and view the created PVC and PV on the **PersistentVolumeClaims (PVCs)** and **PersistentVolumes (PVs)** tab pages.

#. Create an application.

   a. In the navigation pane on the left, click **Workloads**. In the right pane, click the **StatefulSets** tab.

   b. Click **Create Workload** in the upper right corner. On the displayed page, click **Data Storage** in the **Container Settings** area and click **Add Volume** to select **PVC**.

      Mount and use storage volumes, as shown in :ref:`Table 1 <cce_10_0614__table2529244345>`. For details about other parameters, see :ref:`Workloads <cce_10_0046>`.

      .. _cce_10_0614__table2529244345:

      .. table:: **Table 1** Mounting a storage volume

         +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
         +===================================+===========================================================================================================================================================================================================================================================================================================================================================================================================================================================================+
         | PVC                               | Select an existing EVS volume.                                                                                                                                                                                                                                                                                                                                                                                                                                            |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
         |                                   | An EVS volume cannot be repeatedly mounted to multiple workloads.                                                                                                                                                                                                                                                                                                                                                                                                         |
         +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Mount Path                        | Enter a mount path, for example, **/tmp**.                                                                                                                                                                                                                                                                                                                                                                                                                                |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
         |                                   | This parameter indicates the container path to which a data volume will be mounted. Do not mount the volume to a system directory such as **/** or **/var/run**; this action may cause container errors. You are advised to mount the volume to an empty directory. If the directory is not empty, ensure that there are no files that affect container startup. Otherwise, the files will be replaced, causing container startup failures or workload creation failures. |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
         |                                   | .. important::                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
         |                                   |    NOTICE:                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
         |                                   |    If a volume is mounted to a high-risk directory, use an account with minimum permissions to start the container. Otherwise, high-risk files on the host machine may be damaged.                                                                                                                                                                                                                                                                                        |
         +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Subpath                           | Enter a subpath, for example, **tmp**, indicating that data in the mount path of the container will be stored in the **tmp** folder of the volume.                                                                                                                                                                                                                                                                                                                        |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
         |                                   | A subpath is used to mount a local volume so that the same data volume is used in a single pod. If this parameter is left blank, the root path is used by default.                                                                                                                                                                                                                                                                                                        |
         +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Permission                        | -  **Read-only**: You can only read the data in the mounted volumes.                                                                                                                                                                                                                                                                                                                                                                                                      |
         |                                   | -  **Read/Write**: You can modify the data volumes mounted to the path. Newly written data is not migrated if the container is migrated, which may cause data loss.                                                                                                                                                                                                                                                                                                       |
         +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

      In this example, the disk is mounted to the **/data** path of the container. The container data generated in this path is stored in the EVS disk.

      .. note::

         A non-shared EVS disk cannot be attached to multiple pods in a workload. Otherwise, the pods cannot start properly. Ensure that the number of workload pods is 1 when you attach an EVS disk.

   c. After the configuration, click **Create Workload**.

      After the workload is created, the data in the container mount directory will be persistently stored. Verify the storage by referring to :ref:`Verifying Data Persistence <cce_10_0614__section11593165910013>`.

(kubectl) Using an Existing EVS Disk
------------------------------------

#. Use kubectl to connect to the cluster.
#. Create a PV. If a PV has been created in your cluster, skip this step.

   a. .. _cce_10_0614__li162841212145314:

      Create the **pv-evs.yaml** file.

      .. code-block::

         apiVersion: v1
         kind: PersistentVolume
         metadata:
           annotations:
             pv.kubernetes.io/provisioned-by: everest-csi-provisioner
             everest.io/reclaim-policy: retain-volume-only         # (Optional) The PV is deleted while the underlying volume is retained.
           name: pv-evs    # PV name.
           labels:
             failure-domain.beta.kubernetes.io/region: <your_region>   # Region of the node where the application is to be deployed.
             failure-domain.beta.kubernetes.io/zone: <your_zone>       # AZ of the node where the application is to be deployed.
         spec:
           accessModes:
             - ReadWriteOnce     # Access mode. The value is fixed to ReadWriteOnce for EVS disks.
           capacity:
             storage: 10Gi       # EVS disk capacity, in the unit of Gi. The value ranges from 1 to 32768.
           csi:
             driver: disk.csi.everest.io     # Dependent storage driver for the mounting.
             fsType: ext4
             volumeHandle: <your_volume_id>   # Volume ID of the EVS disk.
             volumeAttributes:
               everest.io/disk-mode: SCSI           # Device type of the EVS disk. Only SCSI is supported.
               everest.io/disk-volume-type: SAS     # EVS disk type.
               storage.kubernetes.io/csiProvisionerIdentity: everest-csi-provisioner
               everest.io/crypt-key-id: <your_key_id>    # (Optional) Encryption key ID. Mandatory for an encrypted disk.

           persistentVolumeReclaimPolicy: Delete    # Reclaim policy.
           storageClassName: csi-disk              # Storage class name. The value must be csi-disk for EVS disks.

      .. table:: **Table 2** Key parameters

         +-----------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                                     | Mandatory             | Description                                                                                                                                                                                                                                                                                                               |
         +===============================================+=======================+===========================================================================================================================================================================================================================================================================================================================+
         | everest.io/reclaim-policy: retain-volume-only | No                    | Optional.                                                                                                                                                                                                                                                                                                                 |
         |                                               |                       |                                                                                                                                                                                                                                                                                                                           |
         |                                               |                       | Currently, only **retain-volume-only** is supported.                                                                                                                                                                                                                                                                      |
         |                                               |                       |                                                                                                                                                                                                                                                                                                                           |
         |                                               |                       | This field is valid only when the everest version is 1.2.9 or later and the reclaim policy is **Delete**. If the reclaim policy is **Delete** and the current value is **retain-volume-only**, the associated PV is deleted while the underlying storage volume is retained, when a PVC is deleted.                       |
         +-----------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | failure-domain.beta.kubernetes.io/region      | Yes                   | Region where the cluster is located.                                                                                                                                                                                                                                                                                      |
         |                                               |                       |                                                                                                                                                                                                                                                                                                                           |
         |                                               |                       | For details about the value of **region**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.                                                                                                                                                                                      |
         +-----------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | failure-domain.beta.kubernetes.io/zone        | Yes                   | AZ where the EVS volume is created. It must be the same as the AZ planned for the workload.                                                                                                                                                                                                                               |
         |                                               |                       |                                                                                                                                                                                                                                                                                                                           |
         |                                               |                       | For details about the value of **zone**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.                                                                                                                                                                                        |
         +-----------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | volumeHandle                                  | Yes                   | Volume ID of the EVS disk.                                                                                                                                                                                                                                                                                                |
         |                                               |                       |                                                                                                                                                                                                                                                                                                                           |
         |                                               |                       | To obtain the volume ID, log in to the **Cloud Server Console**. In the navigation pane, choose **Elastic Volume Service** > **Disks**. Click the name of the target EVS disk to go to its details page. On the **Summary** tab page, click the copy button after **ID**.                                                 |
         +-----------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | everest.io/disk-volume-type                   | Yes                   | EVS disk type. All letters are in uppercase.                                                                                                                                                                                                                                                                              |
         |                                               |                       |                                                                                                                                                                                                                                                                                                                           |
         |                                               |                       | -  **SATA**: common I/O                                                                                                                                                                                                                                                                                                   |
         |                                               |                       | -  **SAS**: high I/O                                                                                                                                                                                                                                                                                                      |
         |                                               |                       | -  **SSD**: ultra-high I/O                                                                                                                                                                                                                                                                                                |
         +-----------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | everest.io/crypt-key-id                       | No                    | Mandatory when the EVS disk is encrypted. Enter the encryption key ID selected during EVS disk creation.                                                                                                                                                                                                                  |
         |                                               |                       |                                                                                                                                                                                                                                                                                                                           |
         |                                               |                       | To obtain the encryption key ID, log in to the **Cloud Server Console**. In the navigation pane, choose **Elastic Volume Service** > **Disks**. Click the name of the target EVS disk to go to its details page. On the **Summary** tab page, copy the value of **KMS Key ID** in the **Configuration Information** area. |
         +-----------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | persistentVolumeReclaimPolicy                 | Yes                   | A reclaim policy is supported when the cluster version is or later than 1.19.10 and the everest version is or later than 1.2.9.                                                                                                                                                                                           |
         |                                               |                       |                                                                                                                                                                                                                                                                                                                           |
         |                                               |                       | The **Delete** and **Retain** reclaim policies are supported. For details, see :ref:`PV Reclaim Policy <cce_10_0378__section19999142414413>`. If high data security is required, you are advised to select **Retain** to prevent data from being deleted by mistake.                                                      |
         |                                               |                       |                                                                                                                                                                                                                                                                                                                           |
         |                                               |                       | **Delete**:                                                                                                                                                                                                                                                                                                               |
         |                                               |                       |                                                                                                                                                                                                                                                                                                                           |
         |                                               |                       | -  If **everest.io/reclaim-policy** is not specified, both the PV and EVS volare deleted when a PVC is deleted.                                                                                                                                                                                                           |
         |                                               |                       | -  If **everest.io/reclaim-policy** is set to **retain-volume-only**, when a PVC is deleted, the PV is deleted but the EVS resources are retained.                                                                                                                                                                        |
         |                                               |                       |                                                                                                                                                                                                                                                                                                                           |
         |                                               |                       | **Retain**: When a PVC is deleted, the PV and underlying storage resources are not deleted. Instead, you must manually delete these resources. After that, the PV is in the **Released** status and cannot be bound to the PVC again.                                                                                     |
         +-----------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storageClassName                              | Yes                   | The storage class name for EVS disks is **csi-disk**.                                                                                                                                                                                                                                                                     |
         +-----------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

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
               everest.io/disk-volume-type: SAS    # EVS disk type.
             everest.io/crypt-key-id: <your_key_id>    # (Optional) Encryption key ID. Mandatory for an encrypted disk.

           labels:
             failure-domain.beta.kubernetes.io/region: <your_region>   # Region of the node where the application is to be deployed.
             failure-domain.beta.kubernetes.io/zone: <your_zone>       # AZ of the node where the application is to be deployed.
         spec:
           accessModes:
           - ReadWriteOnce               # The value must be ReadWriteOnce for EVS disks.
           resources:
             requests:
               storage: 10Gi             # EVS disk capacity, ranging from 1 to 32768. The value must be the same as the storage size of the existing PV.
           storageClassName: csi-disk    # Storage class type for EVS disks.
           volumeName: pv-evs            # PV name.

      .. table:: **Table 3** Key parameters

         +------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                                | Mandatory             | Description                                                                                                                          |
         +==========================================+=======================+======================================================================================================================================+
         | failure-domain.beta.kubernetes.io/region | Yes                   | Region where the cluster is located.                                                                                                 |
         |                                          |                       |                                                                                                                                      |
         |                                          |                       | For details about the value of **region**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__. |
         +------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------+
         | failure-domain.beta.kubernetes.io/zone   | Yes                   | AZ where the EVS volume is created. It must be the same as the AZ planned for the workload.                                          |
         |                                          |                       |                                                                                                                                      |
         |                                          |                       | For details about the value of **zone**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.   |
         +------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------+
         | storage                                  | Yes                   | Requested capacity in the PVC, in Gi.                                                                                                |
         |                                          |                       |                                                                                                                                      |
         |                                          |                       | The value must be the same as the storage size of the existing PV.                                                                   |
         +------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------+
         | volumeName                               | Yes                   | PV name, which must be the same as the PV name in :ref:`1 <cce_10_0614__li162841212145314>`.                                         |
         +------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------+
         | storageClassName                         | Yes                   | Storage class name, which must be the same as the storage class of the PV in :ref:`1 <cce_10_0614__li162841212145314>`.              |
         |                                          |                       |                                                                                                                                      |
         |                                          |                       | The storage class name of the EVS volumes is **csi-disk**.                                                                           |
         +------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------+

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
           serviceName: web-evs   # Headless Service name.
           template:
             metadata:
               labels:
                 app: web-evs
             spec:
               containers:
               - name: container-1
                 image: nginx:latest
                 volumeMounts:
                 - name: pvc-disk    # Volume name, which must be the same as the volume name in the volumes field.
                   mountPath: /data  # Location where the storage volume is mounted.
               imagePullSecrets:
                 - name: default-secret
               volumes:
                 - name: pvc-disk    # Volume name, which can be customized.
                   persistentVolumeClaim:
                     claimName: pvc-evs    # Name of the created PVC.
         ---
         apiVersion: v1
         kind: Service
         metadata:
           name: web-evs   # Headless Service name.
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

   c. Run the following command to view the files in the **/data** path:

      .. code-block::

         kubectl exec web-evs-0 -- ls /data

      Expected output:

      .. code-block::

         lost+found

#. Run the following command to create a file named **static** in the **/data** path:

   .. code-block::

      kubectl exec web-evs-0 --  touch /data/static

#. Run the following command to view the files in the **/data** path:

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

   If the **static** file still exists, the data in the EVS volume can be stored persistently.

.. _cce_10_0614__section16505832153318:

Related Operations
------------------

You can also perform the operations listed in :ref:`Table 4 <cce_10_0614__table1619535674020>`.

.. _cce_10_0614__table1619535674020:

.. table:: **Table 4** Related operations

   +---------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Operation                             | Description                                                                                                                                        | Procedure                                                                                                                                                                                                                          |
   +=======================================+====================================================================================================================================================+====================================================================================================================================================================================================================================+
   | Creating a storage volume (PV)        | Create a PV on the CCE console.                                                                                                                    | #. Choose **Storage** from the navigation pane, and click the **PersistentVolumes (PVs)** tab. Click **Create Volume** in the upper right corner. In the dialog box displayed, configure the parameters.                           |
   |                                       |                                                                                                                                                    |                                                                                                                                                                                                                                    |
   |                                       |                                                                                                                                                    |    -  **Volume Type**: Select **EVS**.                                                                                                                                                                                             |
   |                                       |                                                                                                                                                    |    -  **EVS**: Click **Select EVS**. On the displayed page, select the EVS disk that meets your requirements and click **OK**.                                                                                                     |
   |                                       |                                                                                                                                                    |    -  **PV Name**: Enter the PV name, which must be unique in the same cluster.                                                                                                                                                    |
   |                                       |                                                                                                                                                    |    -  **Access Mode**: EVS disks support only **ReadWriteOnce**, indicating that a storage volume can be mounted to one node in read/write mode. For details, see :ref:`Volume Access Modes <cce_10_0378__section43881411172418>`. |
   |                                       |                                                                                                                                                    |    -  **Reclaim Policy**: **Delete** or **Retain**. For details, see :ref:`PV Reclaim Policy <cce_10_0378__section19999142414413>`.                                                                                                |
   |                                       |                                                                                                                                                    |                                                                                                                                                                                                                                    |
   |                                       |                                                                                                                                                    | #. Click **Create**.                                                                                                                                                                                                               |
   +---------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Expanding the capacity of an EVS disk | Quickly expand the capacity of a mounted EVS disk on the CCE console.                                                                              | #. Choose **Storage** from the navigation pane, and click the **PersistentVolumeClaims (PVCs)** tab. Click **More** in the **Operation** column of the target PVC and select **Scale-out**.                                        |
   |                                       |                                                                                                                                                    | #. Enter the capacity to be added and click **OK**.                                                                                                                                                                                |
   +---------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Viewing events                        | You can view event names, event types, number of occurrences, Kubernetes events, first occurrence time, and last occurrence time of the PVC or PV. | #. Choose **Storage** from the navigation pane, and click the **PersistentVolumeClaims (PVCs)** or **PersistentVolumes (PVs)** tab.                                                                                                |
   |                                       |                                                                                                                                                    | #. Click **View Events** in the **Operation** column of the target PVC or PV to view events generated within one hour (event data is retained for one hour).                                                                       |
   +---------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Viewing a YAML file                   | You can view, copy, and download the YAML files of a PVC or PV.                                                                                    | #. Choose **Storage** from the navigation pane, and click the **PersistentVolumeClaims (PVCs)** or **PersistentVolumes (PVs)** tab.                                                                                                |
   |                                       |                                                                                                                                                    | #. Click **View YAML** in the **Operation** column of the target PVC or PV to view or download the YAML.                                                                                                                           |
   +---------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
