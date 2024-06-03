:original_name: cce_10_0619.html

.. _cce_10_0619:

Using an Existing SFS File System Through a Static PV
=====================================================

SFS is a network-attached storage (NAS) that provides shared, scalable, and high-performance file storage. It applies to large-capacity expansion and cost-sensitive services. This section describes how to use an existing SFS file system to statically create PVs and PVCs for data persistence and sharing in workloads.

Prerequisites
-------------

-  You have created a cluster and installed the :ref:`CCE Container Storage (Everest) <cce_10_0066>` add-on in the cluster.
-  Before creating a cluster using commands, ensure kubectl is used to access the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.
-  You have created an SFS file system that is in the same VPC as the cluster.

Constraints
-----------

-  Multiple PVs can use the same SFS or SFS Turbo file system with the following restrictions:

   -  Do not mount all PVCs/PVs that use the same underlying SFS or SFS Turbo file system to a pod. This leads to a pod startup failure because not all PVCs can be mounted to the pod due to the same **volumeHandle** values of these PVs.
   -  The **persistentVolumeReclaimPolicy** parameter in the PVs is suggested to be set to **Retain**. Otherwise, when a PV is deleted, the associated underlying volume may be deleted. In this case, other PVs associated with the underlying volume malfunction.
   -  When the underlying volume is repeatedly used, enable isolation and protection for ReadWriteMany at the application layer to prevent data overwriting and loss.

Using an Existing SFS File System on the Console
------------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Statically create a PVC and PV.

   a. Choose **Storage** in the navigation pane and click the **PVCs** tab. Click **Create PVC** in the upper right corner. In the dialog box displayed, configure the PVC parameters.

      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                         |
      +===================================+=====================================================================================================================================================================================================================+
      | PVC Type                          | In this example, select **SFS**.                                                                                                                                                                                    |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PVC Name                          | Enter the PVC name, which must be unique in the same namespace.                                                                                                                                                     |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Creation Method                   | -  If underlying storage is available, create a storage volume or use an existing storage volume to statically create a PVC based on whether a PV is available.                                                     |
      |                                   | -  If no underlying storage is available, select **Dynamically provision**. For details, see :ref:`Using an SFS File System Through a Dynamic PV <cce_10_0620>`.                                                    |
      |                                   |                                                                                                                                                                                                                     |
      |                                   | In this example, select **Create new** to create a PV and PVC at the same time on the console.                                                                                                                      |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PV\ :sup:`a`                      | Select an existing PV in the cluster. Create a PV in advance. For details, see "Creating a storage volume" in :ref:`Related Operations <cce_10_0619__section16505832153318>`.                                       |
      |                                   |                                                                                                                                                                                                                     |
      |                                   | You do not need to specify this parameter in this example.                                                                                                                                                          |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | SFS\ :sup:`b`                     | Click **Select SFS**. On the displayed page, select the SFS file system that meets your requirements and click **OK**.                                                                                              |
      |                                   |                                                                                                                                                                                                                     |
      |                                   | .. note::                                                                                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                     |
      |                                   |    Currently, only SFS 3.0 Capacity-Oriented is supported.                                                                                                                                                          |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PV Name\ :sup:`b`                 | Enter the PV name, which must be unique in the same cluster.                                                                                                                                                        |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Access Mode\ :sup:`b`             | SFS volumes support only **ReadWriteMany**, indicating that a storage volume can be mounted to multiple nodes in read/write mode. For details, see :ref:`Volume Access Modes <cce_10_0378__section43881411172418>`. |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Reclaim Policy\ :sup:`b`          | You can select **Delete** or **Retain** to specify the reclaim policy of the underlying storage when the PVC is deleted. For details, see :ref:`PV Reclaim Policy <cce_10_0378__section19999142414413>`.            |
      |                                   |                                                                                                                                                                                                                     |
      |                                   | .. note::                                                                                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                     |
      |                                   |    If multiple PVs use the same underlying storage volume, use **Retain** to avoid cascading deletion of underlying volumes.                                                                                        |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Mount Options\ :sup:`b`           | Enter the mounting parameter key-value pairs. For details, see :ref:`Configuring SFS Volume Mount Options <cce_10_0337>`.                                                                                           |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

      .. note::

         a: The parameter is available when **Creation Method** is set to **Use existing**.

         b: The parameter is available when **Creation Method** is set to **Create new**.

   b. Click **Create** to create a PVC and a PV.

      You can choose **Storage** in the navigation pane and view the created PVC and PV on the **PVCs** and **PVs** tab pages, respectively.

#. Create an application.

   a. In the navigation pane on the left, click **Workloads**. In the right pane, click the **Deployments** tab.

   b. Click **Create Workload** in the upper right corner. On the displayed page, click **Data Storage** in the **Container Settings** area and click **Add Volume** to select **PVC**.

      Mount and use storage volumes, as shown in :ref:`Table 1 <cce_10_0619__table2529244345>`. For details about other parameters, see :ref:`Workloads <cce_10_0046>`.

      .. _cce_10_0619__table2529244345:

      .. table:: **Table 1** Mounting a storage volume

         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
         +===================================+=============================================================================================================================================================================================================================================================================================================================================================================================================================================================+
         | PVC                               | Select an existing SFS volume.                                                                                                                                                                                                                                                                                                                                                                                                                              |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Mount Path                        | Enter a mount path, for example, **/tmp**.                                                                                                                                                                                                                                                                                                                                                                                                                  |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
         |                                   | This parameter indicates the container path to which a data volume will be mounted. Do not mount the volume to a system directory such as **/** or **/var/run**. Otherwise, containers will be malfunctional. Mount the volume to an empty directory. If the directory is not empty, ensure that there are no files that affect container startup. Otherwise, the files will be replaced, causing container startup failures or workload creation failures. |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
         |                                   | .. important::                                                                                                                                                                                                                                                                                                                                                                                                                                              |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
         |                                   |    NOTICE:                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
         |                                   |    If a volume is mounted to a high-risk directory, use an account with minimum permissions to start the container. Otherwise, high-risk files on the host machine may be damaged.                                                                                                                                                                                                                                                                          |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Subpath                           | Enter the subpath of the storage volume and mount a path in the storage volume to the container. In this way, different folders of the same storage volume can be used in a single pod. **tmp**, for example, indicates that data in the mount path of the container is stored in the **tmp** folder of the storage volume. If this parameter is left blank, the root path is used by default.                                                              |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Permission                        | -  **Read-only**: You can only read the data in the mounted volumes.                                                                                                                                                                                                                                                                                                                                                                                        |
         |                                   | -  **Read/Write**: You can modify the data volumes mounted to the path. Newly written data will not be migrated if the container is migrated, which may cause data loss.                                                                                                                                                                                                                                                                                    |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

      In this example, the disk is mounted to the **/data** path of the container. The container data generated in this path is stored in the SFS file system.

   c. After the configuration, click **Create Workload**.

      After the workload is created, the data in the container mount directory will be persistently stored. Verify the storage by referring to :ref:`Verifying Data Persistence and Sharing <cce_10_0619__section11593165910013>`.

(kubectl) Using an Existing SFS File System
-------------------------------------------

#. Use kubectl to access the cluster.
#. Create a PV.

   a. .. _cce_10_0619__li162841212145314:

      Create the **pv-sfs.yaml** file.

      SFS Capacity-Oriented:

      .. code-block::

         apiVersion: v1
         kind: PersistentVolume
         metadata:
           annotations:
             pv.kubernetes.io/provisioned-by: everest-csi-provisioner
             everest.io/reclaim-policy: retain-volume-only      # (Optional) The PV is deleted while the underlying volume is retained.
           name: pv-sfs    # PV name.
         spec:
           accessModes:
           - ReadWriteMany      # Access mode. The value must be ReadWriteMany for SFS.
           capacity:
             storage: 1Gi     # SFS volume capacity.
           csi:
             driver: disk.csi.everest.io   # Dependent storage driver for the mounting.
             fsType: nfs
             volumeHandle: <your_volume_id>   # SFS Capacity-Oriented volume ID.
             volumeAttributes:
               everest.io/share-export-location: <your_location>  # Shared path of the SFS volume.
               storage.kubernetes.io/csiProvisionerIdentity: everest-csi-provisioner
           persistentVolumeReclaimPolicy: Retain    # Reclaim policy.
           storageClassName: csi-nas               # Storage class name. csi-nas indicates that SFS Capacity-Oriented is used.
           mountOptions: []                         # Mount options.

      .. table:: **Table 2** Key parameters

         +-----------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                                     | Mandatory             | Description                                                                                                                                                                                                                                                                                         |
         +===============================================+=======================+=====================================================================================================================================================================================================================================================================================================+
         | everest.io/reclaim-policy: retain-volume-only | No                    | Optional.                                                                                                                                                                                                                                                                                           |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       | Currently, only **retain-volume-only** is supported.                                                                                                                                                                                                                                                |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       | This field is valid only when the Everest version is 1.2.9 or later and the reclaim policy is **Delete**. If the reclaim policy is **Delete** and the current value is **retain-volume-only**, the associated PV is deleted while the underlying storage volume is retained, when a PVC is deleted. |
         +-----------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | volumeHandle                                  | Yes                   | -  If an SFS Capacity-Oriented volume is used, enter the volume ID.                                                                                                                                                                                                                                 |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       |    Log in to the CCE console, choose **Service List** > **Storage** > **Scalable File Service**, and select **SFS Turbo**. In the list, click the name of the target SFS file system. On the details page, copy the content following **ID**.                                                       |
         +-----------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | everest.io/share-export-location              | Yes                   | Shared path of the file system.                                                                                                                                                                                                                                                                     |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       | -  For an SFS Capacity-Oriented file system, log in to the CCE console, choose **Service List** > **Storage** > **Scalable File Service**, and obtain the shared path from the **Mount Address** column.                                                                                            |
         +-----------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | mountOptions                                  | Yes                   | Mount options.                                                                                                                                                                                                                                                                                      |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       | If not specified, the following configurations are used by default. For details, see :ref:`Configuring SFS Volume Mount Options <cce_10_0337>`.                                                                                                                                                     |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       | .. code-block::                                                                                                                                                                                                                                                                                     |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       |    mountOptions:                                                                                                                                                                                                                                                                                    |
         |                                               |                       |    - vers=3                                                                                                                                                                                                                                                                                         |
         |                                               |                       |    - timeo=600                                                                                                                                                                                                                                                                                      |
         |                                               |                       |    - nolock                                                                                                                                                                                                                                                                                         |
         |                                               |                       |    - hard                                                                                                                                                                                                                                                                                           |
         +-----------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | persistentVolumeReclaimPolicy                 | Yes                   | A reclaim policy is supported when the cluster version is or later than 1.19.10 and the Everest version is or later than 1.2.9.                                                                                                                                                                     |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       | The **Delete** and **Retain** reclaim policies are supported. For details, see :ref:`PV Reclaim Policy <cce_10_0378__section19999142414413>`. If multiple PVs use the same SFS volume, use **Retain** to prevent the underlying volume from being deleted with a PV.                                |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       | **Delete**:                                                                                                                                                                                                                                                                                         |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       | -  If **everest.io/reclaim-policy** is not specified, both the PV and SFS volume are deleted when a PVC is deleted.                                                                                                                                                                                 |
         |                                               |                       | -  If **everest.io/reclaim-policy** is set to **retain-volume-only**, when a PVC is deleted, the PV is deleted but the SFS volume resources are retained.                                                                                                                                           |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       | **Retain**: When a PVC is deleted, the PV and underlying storage resources are not deleted. Instead, you must manually delete these resources. After that, the PV is in the **Released** status and cannot be bound to the PVC again.                                                               |
         +-----------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storage                                       | Yes                   | Requested capacity in the PVC, in Gi.                                                                                                                                                                                                                                                               |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       | For SFS, this field is used only for verification (cannot be empty or **0**). Its value is fixed at **1**, and any value you set does not take effect for SFS file systems.                                                                                                                         |
         +-----------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   b. Run the following command to create a PV:

      .. code-block::

         kubectl apply -f pv-sfs.yaml

#. Create a PVC.

   a. Create the **pvc-sfs.yaml** file.

      .. code-block::

         apiVersion: v1
         kind: PersistentVolumeClaim
         metadata:
           name: pvc-sfs
           namespace: default
           annotations:
             volume.beta.kubernetes.io/storage-provisioner: everest-csi-provisioner
         spec:
           accessModes:
           - ReadWriteMany               # The value must be ReadWriteMany for SFS.
           resources:
             requests:
               storage: 1Gi               # SFS volume capacity.
         storageClassName: csi-nas # Storage class name, which must be the same as the PV's storage class.
           volumeName: pv-sfs    # PV name.

      .. table:: **Table 3** Key parameters

         +-----------------------+-----------------------+----------------------------------------------------------------------------------------------+
         | Parameter             | Mandatory             | Description                                                                                  |
         +=======================+=======================+==============================================================================================+
         | storage               | Yes                   | Requested capacity in the PVC, in Gi.                                                        |
         |                       |                       |                                                                                              |
         |                       |                       | The value must be the same as the storage size of the existing PV.                           |
         +-----------------------+-----------------------+----------------------------------------------------------------------------------------------+
         | volumeName            | Yes                   | PV name, which must be the same as the PV name in :ref:`1 <cce_10_0619__li162841212145314>`. |
         +-----------------------+-----------------------+----------------------------------------------------------------------------------------------+

   b. Run the following command to create a PVC:

      .. code-block::

         kubectl apply -f pvc-sfs.yaml

#. Create an application.

   a. Create a file named **web-demo.yaml**. In this example, the SFS volume is mounted to the **/data** path.

      .. code-block::

         apiVersion: apps/v1
         kind: Deployment
         metadata:
           name: web-demo
           namespace: default
         spec:
           replicas: 2
           selector:
             matchLabels:
               app: web-demo
           template:
             metadata:
               labels:
                 app: web-demo
             spec:
               containers:
               - name: container-1
                 image: nginx:latest
                 volumeMounts:
                 - name: pvc-sfs-volume    # Volume name, which must be the same as the volume name in the volumes field.
                   mountPath: /data  # Location where the storage volume is mounted.
               imagePullSecrets:
                 - name: default-secret
               volumes:
                 - name: pvc-sfs-volume    # Volume name, which can be customized.
                   persistentVolumeClaim:
                     claimName: pvc-sfs    # Name of the created PVC.

   b. Run the following command to create a workload to which the SFS volume is mounted:

      .. code-block::

         kubectl apply -f web-demo.yaml

      After the workload is created, the data in the container mount directory will be persistently stored. Verify the storage by referring to :ref:`Verifying Data Persistence and Sharing <cce_10_0619__section11593165910013>`.

.. _cce_10_0619__section11593165910013:

Verifying Data Persistence and Sharing
--------------------------------------

#. View the deployed application and files.

   a. Run the following command to view the created pod:

      .. code-block::

         kubectl get pod | grep web-demo

      Expected output:

      .. code-block::

         web-demo-846b489584-mjhm9   1/1     Running   0             46s
         web-demo-846b489584-wvv5s   1/1     Running   0             46s

   b. Run the following commands in sequence to view the files in the **/data** path of the pods:

      .. code-block::

         kubectl exec web-demo-846b489584-mjhm9 -- ls /data
         kubectl exec web-demo-846b489584-wvv5s -- ls /data

      If no result is returned for both pods, no file exists in the **/data** path.

#. Run the following command to create a file named **static** in the **/data** path:

   .. code-block::

      kubectl exec web-demo-846b489584-mjhm9 --  touch /data/static

#. Run the following command to view the files in the **/data** path:

   .. code-block::

      kubectl exec web-demo-846b489584-mjhm9 -- ls /data

   Expected output:

   .. code-block::

      static

#. **Verify data persistence.**

   a. Run the following command to delete the pod named **web-demo-846b489584-mjhm9**:

      .. code-block::

         kubectl delete pod web-demo-846b489584-mjhm9

      Expected output:

      .. code-block::

         pod "web-demo-846b489584-mjhm9" deleted

      After the deletion, the Deployment controller automatically creates a replica.

   b. Run the following command to view the created pod:

      .. code-block::

         kubectl get pod | grep web-demo

      The expected output is as follows, in which **web-demo-846b489584-d4d4j** is the newly created pod:

      .. code-block::

         web-demo-846b489584-d4d4j   1/1     Running   0             110s
         web-demo-846b489584-wvv5s    1/1     Running   0             7m50s

   c. Run the following command to check whether the files in the **/data** path of the new pod have been modified:

      .. code-block::

         kubectl exec web-demo-846b489584-d4d4j -- ls /data

      Expected output:

      .. code-block::

         static

      If the **static** file still exists, the data can be stored persistently.

#. **Verify data sharing.**

   a. Run the following command to view the created pod:

      .. code-block::

         kubectl get pod | grep web-demo

      Expected output:

      .. code-block::

         web-demo-846b489584-d4d4j   1/1     Running   0             7m
         web-demo-846b489584-wvv5s   1/1     Running   0             13m

   b. Run the following command to create a file named **share** in the **/data** path of either pod: In this example, select the pod named **web-demo-846b489584-d4d4j**.

      .. code-block::

         kubectl exec web-demo-846b489584-d4d4j --  touch /data/share

      Check the files in the **/data** path of the pod.

      .. code-block::

         kubectl exec web-demo-846b489584-d4d4j -- ls /data

      Expected output:

      .. code-block::

         share
         static

   c. Check whether the **share** file exists in the **/data** path of another pod (**web-demo-846b489584-wvv5s**) as well to verify data sharing.

      .. code-block::

         kubectl exec web-demo-846b489584-wvv5s -- ls /data

      Expected output:

      .. code-block::

         share
         static

      After you create a file in the **/data** path of a pod, if the file is also created in the **/data** path of the other pod, the two pods share the same volume.

.. _cce_10_0619__section16505832153318:

Related Operations
------------------

You can also perform the operations listed in :ref:`Table 4 <cce_10_0619__table1619535674020>`.

.. _cce_10_0619__table1619535674020:

.. table:: **Table 4** Related operations

   +--------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Operation                      | Description                                                                                                                                        | Procedure                                                                                                                                                                                                                                  |
   +================================+====================================================================================================================================================+============================================================================================================================================================================================================================================+
   | Creating a storage volume (PV) | Create a PV on the CCE console.                                                                                                                    | #. Choose **Storage** in the navigation pane and click the **PVs** tab. Click **Create PersistentVolume** in the upper right corner. In the dialog box displayed, configure the parameters.                                                |
   |                                |                                                                                                                                                    |                                                                                                                                                                                                                                            |
   |                                |                                                                                                                                                    |    -  **Volume Type**: Select **SFS**.                                                                                                                                                                                                     |
   |                                |                                                                                                                                                    |    -  **SFS**: Click **Select SFS**. On the displayed page, select the SFS file system that meets your requirements and click **OK**.                                                                                                      |
   |                                |                                                                                                                                                    |    -  PV Name: Enter the PV name. The PV name must be unique in the same cluster.                                                                                                                                                          |
   |                                |                                                                                                                                                    |    -  **Access Mode**: SFS volumes support only **ReadWriteMany**, indicating that a storage volume can be mounted to multiple nodes in read/write mode. For details, see :ref:`Volume Access Modes <cce_10_0378__section43881411172418>`. |
   |                                |                                                                                                                                                    |    -  **Reclaim Policy**: **Delete** or **Retain**. For details, see :ref:`PV Reclaim Policy <cce_10_0378__section19999142414413>`.                                                                                                        |
   |                                |                                                                                                                                                    |                                                                                                                                                                                                                                            |
   |                                |                                                                                                                                                    |       .. note::                                                                                                                                                                                                                            |
   |                                |                                                                                                                                                    |                                                                                                                                                                                                                                            |
   |                                |                                                                                                                                                    |          If multiple PVs use the same underlying storage volume, use **Retain** to avoid cascading deletion of underlying volumes.                                                                                                         |
   |                                |                                                                                                                                                    |                                                                                                                                                                                                                                            |
   |                                |                                                                                                                                                    |    -  **Mount Options**: Enter the mounting parameter key-value pairs. For details, see :ref:`Configuring SFS Volume Mount Options <cce_10_0337>`.                                                                                         |
   |                                |                                                                                                                                                    |                                                                                                                                                                                                                                            |
   |                                |                                                                                                                                                    | #. Click **Create**.                                                                                                                                                                                                                       |
   +--------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Viewing events                 | You can view event names, event types, number of occurrences, Kubernetes events, first occurrence time, and last occurrence time of the PVC or PV. | #. Choose **Storage** in the navigation pane and click the **PVCs** or **PVs** tab.                                                                                                                                                        |
   |                                |                                                                                                                                                    | #. Click **View Events** in the **Operation** column of the target PVC or PV to view events generated within one hour (event data is retained for one hour).                                                                               |
   +--------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Viewing a YAML file            | You can view, copy, and download the YAML files of a PVC or PV.                                                                                    | #. Choose **Storage** in the navigation pane and click the **PVCs** or **PVs** tab.                                                                                                                                                        |
   |                                |                                                                                                                                                    | #. Click **View YAML** in the **Operation** column of the target PVC or PV to view or download the YAML.                                                                                                                                   |
   +--------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
