:original_name: cce_10_0625.html

.. _cce_10_0625:

Using an Existing SFS Turbo File System Through a Static PV
===========================================================

SFS Turbo is a shared file system with high availability and durability. It is suitable for applications that contain massive small files and require low latency, and high IOPS. This section describes how to use an existing SFS Turbo file system to statically create PVs and PVCs and implement data persistence and sharing in workloads.

Prerequisites
-------------

-  You have created a cluster and installed the CSI add-on (:ref:`everest <cce_10_0066>`) in the cluster.
-  If you want to create a cluster using commands, use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.
-  You have created an available SFS Turbo file system, and the SFS Turbo file system and the cluster are in the same VPC.

Constraints
-----------

-  Multiple PVs can use the same SFS or SFS Turbo file system with the following restrictions:

   -  If multiple PVCs/PVs use the same underlying SFS or SFS Turbo file system, when you attempt to mount these PVCs/PVs to the same pod, all PVCs cannot be mounted to the pod and the pod startup fails. This is because the **volumeHandle** values of these PVs are the same.
   -  The **persistentVolumeReclaimPolicy** parameter in the PVs must be set to **Retain**. Otherwise, when a PV is deleted, the associated underlying volume may be deleted. In this case, other PVs associated with the underlying volume malfunction.
   -  When the underlying volume is repeatedly used, enable isolation and protection for ReadWriteMany at the application layer to prevent data overwriting and loss.

Using an Existing SFS Turbo File System on the Console
------------------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Statically create a PVC and PV.

   a. Choose **Storage** from the navigation pane, and click the **PersistentVolumeClaims (PVCs)** tab. Click **Create PVC** in the upper right corner. In the dialog box displayed, configure the PVC parameters.

      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                               |
      +===================================+===========================================================================================================================================================================================================================+
      | PVC Type                          | In this section, select **SFS Turbo**.                                                                                                                                                                                    |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PVC Name                          | Enter the PVC name, which must be unique in the same namespace.                                                                                                                                                           |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Creation Method                   | You can create a storage volume or use an existing storage volume to statically create a PVC based on whether a PV has been created.                                                                                      |
      |                                   |                                                                                                                                                                                                                           |
      |                                   | In this example, select **Create new** to create a PV and PVC at the same time on the console.                                                                                                                            |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PV\ :sup:`a`                      | Select an existing PV volume in the cluster. Create a PV in advance. For details, see "Creating a storage volume" in :ref:`Related Operations <cce_10_0625__section16505832153318>`.                                      |
      |                                   |                                                                                                                                                                                                                           |
      |                                   | You do not need to specify this parameter in this example.                                                                                                                                                                |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | SFS Turbo\ :sup:`b`               | Click **Select SFS Turbo**. On the displayed page, select the SFS Turbo file system that meets your requirements and click **OK**.                                                                                        |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PV Name\ :sup:`b`                 | Enter the PV name, which must be unique in the same cluster.                                                                                                                                                              |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Access Mode\ :sup:`b`             | SFS Turbo volumes support only **ReadWriteMany**, indicating that a storage volume can be mounted to multiple nodes in read/write mode. For details, see :ref:`Volume Access Modes <cce_10_0378__section43881411172418>`. |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Reclaim Policy\ :sup:`b`          | Only **Retain** is supported, indicating that the PV is not deleted when the PVC is deleted. For details, see :ref:`PV Reclaim Policy <cce_10_0378__section19999142414413>`.                                              |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Mount Options\ :sup:`b`           | Enter the mounting parameter key-value pairs. For details, see :ref:`Configuring SFS Turbo Mount Options <cce_10_0626>`.                                                                                                  |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

      .. note::

         a: The parameter is available when **Creation Method** is set to **Use existing**.

         b: The parameter is available when **Creation Method** is set to **Create new**.

   b. Click **Create** to create a PVC and a PV.

      You can choose **Storage** in the navigation pane and view the created PVC and PV on the **PersistentVolumeClaims (PVCs)** and **PersistentVolumes (PVs)** tab pages.

#. Create an application.

   a. In the navigation pane on the left, click **Workloads**. In the right pane, click the **Deployments** tab.

   b. Click **Create Workload** in the upper right corner. On the displayed page, click **Data Storage** in the **Container Settings** area and click **Add Volume** to select **PVC**.

      Mount and use storage volumes, as shown in :ref:`Table 1 <cce_10_0625__table2529244345>`. For details about other parameters, see :ref:`Workloads <cce_10_0046>`.

      .. _cce_10_0625__table2529244345:

      .. table:: **Table 1** Mounting a storage volume

         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
         +===================================+=============================================================================================================================================================================================================================================================================================================================================================================================================================================================+
         | PVC                               | Select an existing SFS Turbo volume.                                                                                                                                                                                                                                                                                                                                                                                                                        |
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
         | Subpath                           | Enter a subpath, for example, **tmp**, indicating that data in the mount path of the container will be stored in the **tmp** folder of the volume.                                                                                                                                                                                                                                                                                                          |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
         |                                   | A subpath is used to mount a local volume so that the same data volume is used in a single pod. If this parameter is left blank, the root path is used by default.                                                                                                                                                                                                                                                                                          |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Permission                        | -  **Read-only**: You can only read the data in the mounted volumes.                                                                                                                                                                                                                                                                                                                                                                                        |
         |                                   | -  **Read/Write**: You can modify the data volumes mounted to the path. Newly written data is not migrated if the container is migrated, which may cause data loss.                                                                                                                                                                                                                                                                                         |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

      In this example, the disk is mounted to the **/data** path of the container. The container data generated in this path is stored in the SFS Turbo file system.

   c. After the configuration, click **Create Workload**.

      After the workload is created, the data in the container mount directory will be persistently stored. Verify the storage by referring to :ref:`Verifying Data Persistence and Sharing <cce_10_0625__section11593165910013>`.

(kubectl) Using an Existing SFS File System
-------------------------------------------

#. Use kubectl to connect to the cluster.
#. Create a PV.

   a. .. _cce_10_0625__li162841212145314:

      Create the **pv-sfsturbo.yaml** file.

      .. code-block::

         apiVersion: v1
         kind: PersistentVolume
         metadata:
           annotations:
             pv.kubernetes.io/provisioned-by: everest-csi-provisioner
           name: pv-sfsturbo    # PV name.
         spec:
           accessModes:
           - ReadWriteMany      # Access mode. The value must be ReadWriteMany for SFS Turbo.
           capacity:
             storage: 500Gi       # SFS Turbo volume capacity.
           csi:
             driver: sfsturbo.csi.everest.io    # Dependent storage driver for the mounting.
             fsType: nfs
             volumeHandle: <your_volume_id>   # SFS Turbo volume ID.
             volumeAttributes:
               everest.io/share-export-location: <your_location>   # Shared path of the SFS Turbo volume.

               storage.kubernetes.io/csiProvisionerIdentity: everest-csi-provisioner
           persistentVolumeReclaimPolicy: Retain    # Reclaim policy.
           storageClassName: csi-sfsturbo          # Storage class name of the SFS Turbo file system.
           mountOptions: []                         # Mount options.

      .. table:: **Table 2** Key parameters

         +----------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                        | Mandatory             | Description                                                                                                                                                                                                                                            |
         +==================================+=======================+========================================================================================================================================================================================================================================================+
         | volumeHandle                     | Yes                   | SFS Turbo volume ID.                                                                                                                                                                                                                                   |
         |                                  |                       |                                                                                                                                                                                                                                                        |
         |                                  |                       | How to obtain: Log in to the console, choose **Service List** > **Storage** > **Scalable File Service**, and select **SFS Turbo**. In the list, click the name of the target SFS Turbo volume. On the details page, copy the content following **ID**. |
         +----------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | everest.io/share-export-location | Yes                   | Shared path of the SFS Turbo volume.                                                                                                                                                                                                                   |
         |                                  |                       |                                                                                                                                                                                                                                                        |
         |                                  |                       | Log in to the console, choose **Service List** > **Storage** > **Scalable File Service**, and select **SFS Turbo**. You can obtain the shared path of the file system from the **Mount Address** column.                                               |
         +----------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | mountOptions                     | No                    | Mount options.                                                                                                                                                                                                                                         |
         |                                  |                       |                                                                                                                                                                                                                                                        |
         |                                  |                       | If not specified, the following configurations are used by default. For details, see :ref:`Configuring SFS Turbo Mount Options <cce_10_0626>`.                                                                                                         |
         |                                  |                       |                                                                                                                                                                                                                                                        |
         |                                  |                       | .. code-block::                                                                                                                                                                                                                                        |
         |                                  |                       |                                                                                                                                                                                                                                                        |
         |                                  |                       |    mountOptions:                                                                                                                                                                                                                                       |
         |                                  |                       |    - vers=3                                                                                                                                                                                                                                            |
         |                                  |                       |    - timeo=600                                                                                                                                                                                                                                         |
         |                                  |                       |    - nolock                                                                                                                                                                                                                                            |
         |                                  |                       |    - hard                                                                                                                                                                                                                                              |
         +----------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | persistentVolumeReclaimPolicy    | Yes                   | A reclaim policy is supported when the cluster version is or later than 1.19.10 and the everest version is or later than 1.2.9.                                                                                                                        |
         |                                  |                       |                                                                                                                                                                                                                                                        |
         |                                  |                       | Only the **Retain** reclaim policy is supported. For details, see :ref:`Verifying Data Persistence and Sharing <cce_10_0625__section11593165910013>`.                                                                                                  |
         |                                  |                       |                                                                                                                                                                                                                                                        |
         |                                  |                       | **Retain**: When a PVC is deleted, the PV and underlying storage resources are not deleted. Instead, you must manually delete these resources. After that, the PV is in the **Released** status and cannot be bound to the PVC again.                  |
         +----------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storage                          | Yes                   | Requested capacity in the PVC, in Gi.                                                                                                                                                                                                                  |
         +----------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storageClassName                 | Yes                   | The storage class name of SFS Turbo volumes is **csi-sfsturbo**.                                                                                                                                                                                       |
         +----------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   b. Run the following command to create a PV:

      .. code-block::

         kubectl apply -f pv-sfsturbo.yaml

#. Create a PVC.

   a. Create the **pvc-sfsturbo.yaml** file.

      .. code-block::

         apiVersion: v1
         kind: PersistentVolumeClaim
         metadata:
           name: pvc-sfsturbo
           namespace: default
           annotations:
             volume.beta.kubernetes.io/storage-provisioner: everest-csi-provisioner

         spec:
           accessModes:
           - ReadWriteMany                  # The value must be ReadWriteMany for SFS Turbo.
           resources:
             requests:
               storage: 500Gi               # SFS Turbo volume capacity.
           storageClassName: csi-sfsturbo       # Storage class of the SFS Turbo volume, which must be the same as that of the PV.
           volumeName: pv-sfsturbo    # PV name.

      .. table:: **Table 3** Key parameters

         +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------+
         | Parameter             | Mandatory             | Description                                                                                                             |
         +=======================+=======================+=========================================================================================================================+
         | storage               | Yes                   | Requested capacity in the PVC, in Gi.                                                                                   |
         |                       |                       |                                                                                                                         |
         |                       |                       | The value must be the same as the storage size of the existing PV.                                                      |
         +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------+
         | storageClassName      | Yes                   | Storage class name, which must be the same as the storage class of the PV in :ref:`1 <cce_10_0625__li162841212145314>`. |
         |                       |                       |                                                                                                                         |
         |                       |                       | The storage class name of SFS Turbo volumes is **csi-sfsturbo**.                                                        |
         +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------+
         | volumeName            | Yes                   | PV name, which must be the same as the PV name in :ref:`1 <cce_10_0625__li162841212145314>`.                            |
         +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------+

   b. Run the following command to create a PVC:

      .. code-block::

         kubectl apply -f pvc-sfsturbo.yaml

#. Create an application.

   a. Create a file named **web-demo.yaml**. In this example, the SFS Turbo volume is mounted to the **/data** path.

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
                 - name: pvc-sfsturbo-volume    #Volume name, which must be the same as the volume name in the volumes field.
                   mountPath: /data  #Location where the storage volume is mounted.
               imagePullSecrets:
                 - name: default-secret
               volumes:
                 - name: pvc-sfsturbo-volume    #Volume name, which can be customized.
                   persistentVolumeClaim:
                     claimName: pvc-sfsturbo    #Name of the created PVC.

   b. Run the following command to create an application to which the SFS Turbo volume is mounted:

      .. code-block::

         kubectl apply -f web-demo.yaml

      After the workload is created, you can try :ref:`Verifying Data Persistence and Sharing <cce_10_0625__section11593165910013>`.

.. _cce_10_0625__section11593165910013:

Verifying Data Persistence and Sharing
--------------------------------------

#. View the deployed applications and files.

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

      After you create a file in the **/data** path of a pod, if the file is also created in the **/data** path of another pods, the two pods share the same volume.

.. _cce_10_0625__section16505832153318:

Related Operations
------------------

You can also perform the operations listed in :ref:`Table 4 <cce_10_0625__table1619535674020>`.

.. _cce_10_0625__table1619535674020:

.. table:: **Table 4** Related operations

   +-----------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Operation                                     | Description                                                                                                                                        | Procedure                                                                                                                                                                                                                                  |
   +===============================================+====================================================================================================================================================+============================================================================================================================================================================================================================================+
   | Creating a storage volume (PV)                | Create a PV on the CCE console.                                                                                                                    | #. Choose **Storage** from the navigation pane, and click the **PersistentVolumes (PVs)** tab. Click **Create Volume** in the upper right corner. In the dialog box displayed, configure the parameters.                                   |
   |                                               |                                                                                                                                                    |                                                                                                                                                                                                                                            |
   |                                               |                                                                                                                                                    |    -  **Volume Type**: Select **SFS Turbo**.                                                                                                                                                                                               |
   |                                               |                                                                                                                                                    |    -  **SFS Turbo**: Click **Select SFS Turbo**. On the page displayed, select the SFS Turbo volume that meets the requirements and click **OK**.                                                                                          |
   |                                               |                                                                                                                                                    |    -  **PV Name**: Enter the PV name, which must be unique in the same cluster.                                                                                                                                                            |
   |                                               |                                                                                                                                                    |    -  **Access Mode**: SFS volumes support only **ReadWriteMany**, indicating that a storage volume can be mounted to multiple nodes in read/write mode. For details, see :ref:`Volume Access Modes <cce_10_0378__section43881411172418>`. |
   |                                               |                                                                                                                                                    |    -  **Reclaim Policy**: Only **Retain** is supported. For details, see :ref:`PV Reclaim Policy <cce_10_0378__section19999142414413>`.                                                                                                    |
   |                                               |                                                                                                                                                    |    -  **Mount Options**: Enter the mounting parameter key-value pairs. For details, see :ref:`Configuring SFS Turbo Mount Options <cce_10_0626>`.                                                                                          |
   |                                               |                                                                                                                                                    |                                                                                                                                                                                                                                            |
   |                                               |                                                                                                                                                    | #. Click **Create**.                                                                                                                                                                                                                       |
   +-----------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Expanding the capacity of an SFS Turbo volume | Quickly expand the capacity of a mounted SFS Turbo volume on the CCE console.                                                                      | #. Choose **Storage** from the navigation pane, and click the **PersistentVolumeClaims (PVCs)** tab. Click **More** in the **Operation** column of the target PVC and select **Scale-out**.                                                |
   |                                               |                                                                                                                                                    | #. Enter the capacity to be added and click **OK**.                                                                                                                                                                                        |
   +-----------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Viewing events                                | You can view event names, event types, number of occurrences, Kubernetes events, first occurrence time, and last occurrence time of the PVC or PV. | #. Choose **Storage** from the navigation pane, and click the **PersistentVolumeClaims (PVCs)** or **PersistentVolumes (PVs)** tab.                                                                                                        |
   |                                               |                                                                                                                                                    | #. Click **View Events** in the **Operation** column of the target PVC or PV to view events generated within one hour (event data is retained for one hour).                                                                               |
   +-----------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Viewing a YAML file                           | You can view, copy, and download the YAML files of a PVC or PV.                                                                                    | #. Choose **Storage** from the navigation pane, and click the **PersistentVolumeClaims (PVCs)** or **PersistentVolumes (PVs)** tab.                                                                                                        |
   |                                               |                                                                                                                                                    | #. Click **View YAML** in the **Operation** column of the target PVC or PV to view or download the YAML.                                                                                                                                   |
   +-----------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
