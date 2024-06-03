:original_name: cce_10_0634.html

.. _cce_10_0634:

Using a Local PV Through a Dynamic PV
=====================================

Prerequisites
-------------

-  You have created a cluster and installed the CSI add-on (:ref:`Everest <cce_10_0066>`) in the cluster.
-  Before creating a cluster using commands, ensure kubectl is used to access the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.
-  You have imported a data disk of a node to the local PV storage pool. For details, see :ref:`Importing a PV to a Storage Pool <cce_10_0642>`.

Constraints
-----------

-  Local PVs are supported only when the cluster version is v1.21.2-r0 or later and the Everest add-on version is 2.1.23 or later. Version 2.1.23 or later is recommended.
-  Deleting, removing, resetting, or scaling in a node will cause the PVC/PV data of the local PV associated with the node to be lost, which cannot be restored or used again. For details, see :ref:`Removing a Node <cce_10_0338>`, :ref:`Deleting a Node <cce_10_0186>`, :ref:`Resetting a Node <cce_10_0003>`, and :ref:`Scaling a Node <cce_10_0291>`. In these scenarios, the pod that uses the local PV is evicted from the node. A new pod will be created and stay in the pending state. This is because the PVC used by the pod has a node label, due to which the pod cannot be scheduled. After the node is reset, the pod may be scheduled to the reset node. In this case, the pod remains in the creating state because the underlying logical volume corresponding to the PVC does not exist.
-  Do not manually delete the corresponding storage pool or detach data disks from the node. Otherwise, exceptions such as data loss may occur.
-  Local PVs are in non-shared mode and cannot be mounted to multiple workloads or tasks concurrently. Additionally, local PVs cannot be mounted to multiple pods of a workload concurrently.

Automatically Creating a Local PV on the Console
------------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Dynamically create a PVC and PV.

   a. Choose **Storage** in the navigation pane and click the **PVCs** tab. Click **Create PVC** in the upper right corner. In the dialog box displayed, configure the PVC parameters.

      +-----------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter       | Description                                                                                                                                                                                                 |
      +=================+=============================================================================================================================================================================================================+
      | PVC Type        | In this section, select **Local PV**.                                                                                                                                                                       |
      +-----------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PVC Name        | Enter the PVC name, which must be unique in the same namespace.                                                                                                                                             |
      +-----------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Creation Method | You can only select **Dynamically provision** to create a PVC, PV, and underlying storage on the console in cascading mode.                                                                                 |
      +-----------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Storage Classes | The storage class of local PVs is **csi-local-topology**.                                                                                                                                                   |
      +-----------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Access Mode     | Local PVs support only **ReadWriteOnce**, indicating that a storage volume can be mounted to one node in read/write mode. For details, see :ref:`Volume Access Modes <cce_10_0378__section43881411172418>`. |
      +-----------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Storage Pool    | View the imported storage pool. For details about how to import a new data volume to the storage pool, see :ref:`Importing a PV to a Storage Pool <cce_10_0642>`.                                           |
      +-----------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Capacity (GiB)  | Capacity of the requested storage volume.                                                                                                                                                                   |
      +-----------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   b. Click **Create** to create a PVC and a PV.

      You can choose **Storage** in the navigation pane and view the created PVC and PV on the **PVCs** and **PVs** tab pages, respectively.

      .. note::

         The volume binding mode of the local storage class (named **csi-local-topology**) is late binding (that is, the value of **volumeBindingMode** is **WaitForFirstConsumer**). In this mode, PV creation and binding are delayed. The corresponding PV is created and bound only when the PVC is used during workload creation.

#. Create an application.

   a. In the navigation pane on the left, click **Workloads**. In the right pane, click the **Deployments** tab.

   b. Click **Create Workload** in the upper right corner. On the displayed page, click **Data Storage** in the **Container Settings** area and click **Add Volume** to select **PVC**.

      Mount and use storage volumes, as shown in :ref:`Table 1 <cce_10_0634__table2529244345>`. For details about other parameters, see :ref:`Workloads <cce_10_0046>`.

      .. _cce_10_0634__table2529244345:

      .. table:: **Table 1** Mounting a storage volume

         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
         +===================================+=============================================================================================================================================================================================================================================================================================================================================================================================================================================================+
         | PVC                               | Select an existing local PV.                                                                                                                                                                                                                                                                                                                                                                                                                                |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
         |                                   | A local PV cannot be repeatedly mounted to multiple workloads.                                                                                                                                                                                                                                                                                                                                                                                              |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Mount Path                        | Enter a mount path, for example, **/tmp**.                                                                                                                                                                                                                                                                                                                                                                                                                  |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
         |                                   | This parameter indicates the container path to which a data volume will be mounted. Do not mount the volume to a system directory such as **/** or **/var/run**. Otherwise, containers will be malfunctional. Mount the volume to an empty directory. If the directory is not empty, ensure that there are no files that affect container startup. Otherwise, the files will be replaced, causing container startup failures or workload creation failures. |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
         |                                   | .. important::                                                                                                                                                                                                                                                                                                                                                                                                                                              |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
         |                                   |    NOTICE:                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
         |                                   |    If the container is mounted to a high-risk directory, use an account with minimum permissions to start the container. Otherwise, high-risk files on the host may be damaged.                                                                                                                                                                                                                                                                             |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Subpath                           | Enter the subpath of the storage volume and mount a path in the storage volume to the container. In this way, different folders of the same storage volume can be used in a single pod. **tmp**, for example, indicates that data in the mount path of the container is stored in the **tmp** folder of the storage volume. If this parameter is left blank, the root path is used by default.                                                              |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Permission                        | -  **Read-only**: You can only read the data in the mounted volumes.                                                                                                                                                                                                                                                                                                                                                                                        |
         |                                   | -  **Read/Write**: You can modify the data volumes mounted to the path. Newly written data will not be migrated if the container is migrated, which may cause data loss.                                                                                                                                                                                                                                                                                    |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

      In this example, the disk is mounted to the **/data** path of the container. The container data generated in this path is stored in the local PV.

   c. After the configuration, click **Create Workload**.

      After the workload is created, the data in the container mount directory will be persistently stored. Verify the storage by referring to :ref:`Verifying Data Persistence <cce_10_0634__section11593165910013>`.

(kubectl) Automatically Creating a Local PV
-------------------------------------------

#. Use kubectl to access the cluster.
#. Use **StorageClass** to dynamically create a PVC and PV.

   a. Create the **pvc-local.yaml** file.

      .. code-block::

         apiVersion: v1
         kind: PersistentVolumeClaim
         metadata:
           name: pvc-local
           namespace: default
         spec:
           accessModes:
             - ReadWriteOnce             # The value must be ReadWriteOnce for local PVs.
           resources:
             requests:
               storage: 10Gi             # Size of the local PV.
           storageClassName: csi-local-topology    # StorageClass is local PV.

      .. table:: **Table 2** Key parameters

         +------------------+-----------+-----------------------------------------------------------------------------------+
         | Parameter        | Mandatory | Description                                                                       |
         +==================+===========+===================================================================================+
         | storage          | Yes       | Requested capacity in the PVC, in Gi.                                             |
         +------------------+-----------+-----------------------------------------------------------------------------------+
         | storageClassName | Yes       | Storage class name. The storage class name of local PV is **csi-local-topology**. |
         +------------------+-----------+-----------------------------------------------------------------------------------+

   b. Run the following command to create a PVC:

      .. code-block::

         kubectl apply -f pvc-local.yaml

#. Create an application.

   a. Create a file named **web-demo.yaml**. In this example, the local PV is mounted to the **/data** path.

      .. code-block::

         apiVersion: apps/v1
         kind: StatefulSet
         metadata:
           name: web-local
           namespace: default
         spec:
           replicas: 1
           selector:
             matchLabels:
               app: web-local
           serviceName: web-local   # Headless Service name.
           template:
             metadata:
               labels:
                 app: web-local
             spec:
               containers:
               - name: container-1
                 image: nginx:latest
                 volumeMounts:
                 - name: pvc-disk    #Volume name, which must be the same as the volume name in the volumes field.
                   mountPath: /data  #Location where the storage volume is mounted.
               imagePullSecrets:
                 - name: default-secret
               volumes:
                 - name: pvc-disk    #Volume name, which can be customized.
                   persistentVolumeClaim:
                     claimName: pvc-local    #Name of the created PVC.
         ---
         apiVersion: v1
         kind: Service
         metadata:
           name: web-local   # Headless Service name.
           namespace: default
           labels:
             app: web-local
         spec:
           selector:
             app: web-local
           clusterIP: None
           ports:
             - name: web-local
               targetPort: 80
               nodePort: 0
               port: 80
               protocol: TCP
           type: ClusterIP

   b. Run the following command to create a workload to which the local PV is mounted:

      .. code-block::

         kubectl apply -f web-local.yaml

      After the workload is created, the data in the container mount directory will be persistently stored. Verify the storage by referring to :ref:`Verifying Data Persistence <cce_10_0634__section11593165910013>`.

.. _cce_10_0634__section11593165910013:

Verifying Data Persistence
--------------------------

#. View the deployed application and local files.

   a. Run the following command to view the created pod:

      .. code-block::

         kubectl get pod | grep web-local

      Expected output:

      .. code-block::

         web-local-0                  1/1     Running   0               38s

   b. Run the following command to check whether the local PV has been mounted to the **/data** path:

      .. code-block::

         kubectl exec web-local-0 -- df | grep data

      Expected output:

      .. code-block::

         /dev/mapper/vg--everest--localvolume--persistent-pvc-local          10255636     36888  10202364   0% /data

   c. Run the following command to view the files in the **/data** path:

      .. code-block::

         kubectl exec web-local-0 -- ls /data

      Expected output:

      .. code-block::

         lost+found

#. Run the following command to create a file named **static** in the **/data** path:

   .. code-block::

      kubectl exec web-local-0 --  touch /data/static

#. Run the following command to view the files in the **/data** path:

   .. code-block::

      kubectl exec web-local-0 -- ls /data

   Expected output:

   .. code-block::

      lost+found
      static

#. Run the following command to delete the pod named **web-local-0**:

   .. code-block::

      kubectl delete pod web-local-0

   Expected output:

   .. code-block::

      pod "web-local-0" deleted

#. After the deletion, the StatefulSet controller automatically creates a replica with the same name. Run the following command to check whether the files in the **/data** path have been modified:

   .. code-block::

      kubectl exec web-local-0 -- ls /data

   Expected output:

   .. code-block::

      lost+found
      static

   If the **static** file still exists, the data in the local PV can be stored persistently.

Related Operations
------------------

You can also perform the operations listed in :ref:`Table 3 <cce_10_0634__table1619535674020>`.

.. _cce_10_0634__table1619535674020:

.. table:: **Table 3** Related operations

   +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Operation             | Description                                                                                                                                        | Procedure                                                                                                                                                    |
   +=======================+====================================================================================================================================================+==============================================================================================================================================================+
   | Viewing events        | You can view event names, event types, number of occurrences, Kubernetes events, first occurrence time, and last occurrence time of the PVC or PV. | #. Choose **Storage** in the navigation pane and click the **PVCs** or **PVs** tab.                                                                          |
   |                       |                                                                                                                                                    | #. Click **View Events** in the **Operation** column of the target PVC or PV to view events generated within one hour (event data is retained for one hour). |
   +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Viewing a YAML file   | You can view, copy, and download the YAML files of a PVC or PV.                                                                                    | #. Choose **Storage** in the navigation pane and click the **PVCs** or **PVs** tab.                                                                          |
   |                       |                                                                                                                                                    | #. Click **View YAML** in the **Operation** column of the target PVC or PV to view or download the YAML.                                                     |
   +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------+
