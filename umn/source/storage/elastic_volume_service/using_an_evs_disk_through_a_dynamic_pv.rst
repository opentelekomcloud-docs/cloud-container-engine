:original_name: cce_10_0615.html

.. _cce_10_0615:

Using an EVS Disk Through a Dynamic PV
======================================

CCE allows you to specify a StorageClass to automatically create an EVS disk and the corresponding PV. This function is applicable when no underlying storage volume is available.

Prerequisites
-------------

-  You have created a cluster and installed the :ref:`CCE Container Storage (Everest) <cce_10_0066>` add-on in the cluster.
-  If you want to create a cluster using commands, use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

Constraints
-----------

-  EVS disks cannot be attached across AZs and cannot be used by multiple workloads, multiple pods of the same workload, or multiple tasks. Data sharing of a shared disk is not supported between nodes in a CCE cluster. If an EVS disk is attacked to multiple nodes, I/O conflicts and data cache conflicts may occur. Therefore, create only one pod when creating a Deployment that uses EVS disks.

-  For clusters earlier than v1.19.10, if an HPA policy is used to scale out a workload with EVS disks attached, the existing pods cannot be read or written when a new pod is scheduled to another node.

   For clusters of v1.19.10 and later, if an HPA policy is used to scale out a workload with EVS disks attached, a new pod cannot be started because EVS disks cannot be attached.

(Console) Automatically Creating an EVS Disk
--------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Dynamically create a PVC and PV.

   a. Choose **Storage** in the navigation pane and click the **PersistentVolumeClaims (PVCs)** tab. Click **Create PVC** in the upper right corner. In the dialog box displayed, configure the PVC parameters.

      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                   |
      +===================================+===============================================================================================================================================================================================================================+
      | PVC Type                          | In this example, select **EVS**.                                                                                                                                                                                              |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PVC Name                          | Enter the PVC name, which must be unique in the same namespace.                                                                                                                                                               |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Creation Method                   | -  If no underlying storage is available, select **Dynamically provision** to create a PVC, PV, and underlying storage on the console in cascading mode.                                                                      |
      |                                   | -  If underlying storage is available, create a PV or use an existing PV to statically create a PVC based on whether a PV is available. For details, see :ref:`Using an Existing EVS Disk Through a Static PV <cce_10_0614>`. |
      |                                   |                                                                                                                                                                                                                               |
      |                                   | In this example, select **Dynamically provision**.                                                                                                                                                                            |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Storage Classes                   | The storage class for EVS disks is **csi-disk**.                                                                                                                                                                              |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | AZ                                | Select the AZ of the EVS disk. The AZ must be the same as that of the cluster node.                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                               |
      |                                   | .. note::                                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                               |
      |                                   |    An EVS disk can only be mounted to a node in the same AZ. After an EVS disk is created, its AZ cannot be changed.                                                                                                          |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Disk Type                         | Select an EVS disk type.                                                                                                                                                                                                      |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Access Mode                       | EVS disks support only **ReadWriteOnce**, indicating that a storage volume can be mounted to one node in read/write mode. For details, see :ref:`Volume Access Modes <cce_10_0378__section43881411172418>`.                   |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Capacity (GiB)                    | Capacity of the requested storage volume.                                                                                                                                                                                     |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Encryption                        | You can select **Encryption** and an encryption key to encrypt underlying storage. Before using the encryption function, check whether the region where the EVS disk is located supports disk encryption.                     |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Resource Tag                      | You can add resource tags to classify resources, which is supported only when the Everest version in the cluster is 2.1.39 or later.                                                                                          |
      |                                   |                                                                                                                                                                                                                               |
      |                                   | You can create **predefined tags** on the TMS console. The predefined tags are available to all resources that support tags. You can use predefined tags to improve the tag creation and resource migration efficiency.       |
      |                                   |                                                                                                                                                                                                                               |
      |                                   | CCE automatically creates system tags **CCE-Cluster-ID=**\ *{Cluster ID}*, **CCE-Cluster-Name=**\ *{Cluster name}*, and **CCE-Namespace=**\ *{Namespace name}*. These tags cannot be modified.                                |
      |                                   |                                                                                                                                                                                                                               |
      |                                   | .. note::                                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                               |
      |                                   |    After a dynamic PV of the EVS type is created, the resource tags cannot be updated on the CCE console. To update these resource tags, go to the EVS console.                                                               |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   b. Click **Create**.

      You can choose **Storage** in the navigation pane and view the created PVC and PV on the **PersistentVolumeClaims (PVCs)** and **PersistentVolumes (PVs)** tab pages, respectively.

#. Create an application.

   a. In the navigation pane on the left, click **Workloads**. In the right pane, click the **StatefulSets** tab.

   b. Click **Create Workload** in the upper right corner. On the displayed page, click **Data Storage** in the **Container Settings** area and click **Add Volume** to select **PVC**.

      Mount and use storage volumes, as shown in :ref:`Table 1 <cce_10_0615__cce_10_0614_table2529244345>`. For details about other parameters, see :ref:`Workloads <cce_10_0046>`.

      .. _cce_10_0615__cce_10_0614_table2529244345:

      .. table:: **Table 1** Mounting a storage volume

         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
         +===================================+=============================================================================================================================================================================================================================================================================================================================================================================================================================================================+
         | PVC                               | Select an existing EVS volume.                                                                                                                                                                                                                                                                                                                                                                                                                              |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
         |                                   | An EVS volume cannot be repeatedly mounted to multiple workloads.                                                                                                                                                                                                                                                                                                                                                                                           |
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

      In this example, the disk is mounted to the **/data** path of the container. The container data generated in this path is stored in the EVS disk.

      .. note::

         A non-shared EVS disk cannot be attached to multiple pods in a workload. Otherwise, the pods cannot start properly. Ensure that the number of workload pods is 1 when you attach an EVS disk.

   c. After the configuration, click **Create Workload**.

      After the workload is created, the data in the container mount directory will be persistently stored. Verify the storage by referring to :ref:`Verifying Data Persistence <cce_10_0614__section11593165910013>`.

(kubectl) Automatically Creating an EVS Disk
--------------------------------------------

#. Use kubectl to connect to the cluster.
#. Use **StorageClass** to dynamically create a PVC and PV.

   a. Create the **pvc-evs-auto.yaml** file.

      .. code-block::

         apiVersion: v1
         kind: PersistentVolumeClaim
         metadata:
           name: pvc-evs-auto
           namespace: default
           annotations:
               everest.io/disk-volume-type: SAS    # EVS disk type.
             everest.io/crypt-key-id: <your_key_id>    # (Optional) Encryption key ID. Mandatory for an encrypted disk.

             everest.io/disk-volume-tags: '{"key1":"value1","key2":"value2"}' # (Optional) Custom resource tags
             csi.storage.k8s.io/fstype: xfs    # (Optional) Set the file system type to xfs. If it is left blank, ext4 is used by default.
           labels:
             failure-domain.beta.kubernetes.io/region: <your_region>   # Region of the node where the application is to be deployed.
             failure-domain.beta.kubernetes.io/zone: <your_zone>       # AZ of the node where the application is to be deployed.
         spec:
           accessModes:
           - ReadWriteOnce               # The value must be ReadWriteOnce for EVS disks.
           resources:
             requests:
               storage: 10Gi             # EVS disk capacity, ranging from 1 to 32768.
           storageClassName: csi-disk    # Storage class type for EVS disks.

      .. table:: **Table 2** Key parameters

         +------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                                | Mandatory             | Description                                                                                                                                                                                                             |
         +==========================================+=======================+=========================================================================================================================================================================================================================+
         | failure-domain.beta.kubernetes.io/region | Yes                   | Region where the cluster is located.                                                                                                                                                                                    |
         |                                          |                       |                                                                                                                                                                                                                         |
         |                                          |                       | For details about the value of **region**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.                                                                                    |
         +------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | failure-domain.beta.kubernetes.io/zone   | Yes                   | AZ where the EVS volume is created. It must be the same as the AZ planned for the workload.                                                                                                                             |
         |                                          |                       |                                                                                                                                                                                                                         |
         |                                          |                       | For details about the value of **zone**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.                                                                                      |
         +------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | everest.io/disk-volume-type              | Yes                   | EVS disk type. All letters are in uppercase.                                                                                                                                                                            |
         |                                          |                       |                                                                                                                                                                                                                         |
         |                                          |                       | -  **SATA**: common I/O                                                                                                                                                                                                 |
         |                                          |                       | -  **SAS**: high I/O                                                                                                                                                                                                    |
         |                                          |                       | -  **SSD**: ultra-high I/O                                                                                                                                                                                              |
         +------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | everest.io/crypt-key-id                  | No                    | This parameter is mandatory when an EVS disk is encrypted. Enter the encryption key ID selected during EVS disk creation. You can use a custom key or the default key named **evs/default**.                            |
         |                                          |                       |                                                                                                                                                                                                                         |
         |                                          |                       | To obtain a key ID, log in to the DEW console, locate the key to be encrypted, and copy the key ID.                                                                                                                     |
         +------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | everest.io/disk-volume-tags              | No                    | This field is optional. It is supported when the Everest version in the cluster is 2.1.39 or later.                                                                                                                     |
         |                                          |                       |                                                                                                                                                                                                                         |
         |                                          |                       | You can add resource tags to classify resources.                                                                                                                                                                        |
         |                                          |                       |                                                                                                                                                                                                                         |
         |                                          |                       | You can create **predefined tags** on the TMS console. The predefined tags are available to all resources that support tags. You can use predefined tags to improve the tag creation and resource migration efficiency. |
         |                                          |                       |                                                                                                                                                                                                                         |
         |                                          |                       | CCE automatically creates system tags **CCE-Cluster-ID=**\ *{Cluster ID}*, **CCE-Cluster-Name=**\ *{Cluster name}*, and **CCE-Namespace=**\ *{Namespace name}*. These tags cannot be modified.                          |
         +------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | csi.storage.k8s.io/fstype                | No                    | This field is optional. It specifies the file system type. The default value is **ext4**.                                                                                                                               |
         |                                          |                       |                                                                                                                                                                                                                         |
         |                                          |                       | The value can be **ext4** or **xfs**. The restrictions on using xfs are as follows:                                                                                                                                     |
         |                                          |                       |                                                                                                                                                                                                                         |
         |                                          |                       | -  The nodes should run CentOS 7 or Ubuntu 22.04, and the Everest version in the cluster should be 2.3.2 or later.                                                                                                      |
         |                                          |                       | -  Only common containers are supported.                                                                                                                                                                                |
         +------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storage                                  | Yes                   | Requested PVC capacity, in Gi. The value ranges from **1** to **32768**.                                                                                                                                                |
         +------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storageClassName                         | Yes                   | The storage class name of the EVS volumes is **csi-disk**.                                                                                                                                                              |
         +------------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   b. Run the following command to create a PVC:

      .. code-block::

         kubectl apply -f pvc-evs-auto.yaml

#. Create an application.

   a. Create a file named **web-evs-auto.yaml**. In this example, the EVS volume is mounted to the **/data** path.

      .. code-block::

         apiVersion: apps/v1
         kind: StatefulSet
         metadata:
           name: web-evs-auto
           namespace: default
         spec:
           replicas: 1
           selector:
             matchLabels:
               app: web-evs-auto
           serviceName: web-evs-auto   # Headless Service name.
           template:
             metadata:
               labels:
                 app: web-evs-auto
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
                     claimName: pvc-evs-auto    # Name of the created PVC.
         ---
         apiVersion: v1
         kind: Service
         metadata:
           name: web-evs-auto   # Headless Service name.
           namespace: default
           labels:
             app: web-evs-auto
         spec:
           selector:
             app: web-evs-auto
           clusterIP: None
           ports:
             - name: web-evs-auto
               targetPort: 80
               nodePort: 0
               port: 80
               protocol: TCP
           type: ClusterIP

   b. Run the following command to create a workload to which the EVS volume is mounted:

      .. code-block::

         kubectl apply -f web-evs-auto.yaml

      After the workload is created, the data in the container mount directory will be persistently stored. Verify the storage by referring to :ref:`Verifying Data Persistence <cce_10_0615__section11593165910013>`.

.. _cce_10_0615__section11593165910013:

Verifying Data Persistence
--------------------------

#. View the deployed application and EVS volume files.

   a. Run the following command to view the created pod:

      .. code-block::

         kubectl get pod | grep web-evs-auto

      Expected output:

      .. code-block::

         web-evs-auto-0                  1/1     Running   0               38s

   b. Run the following command to check whether the EVS volume has been mounted to the **/data** path:

      .. code-block::

         kubectl exec web-evs-auto-0 -- df | grep data

      Expected output:

      .. code-block::

         /dev/sdc              10255636     36888  10202364   0% /data

   c. Run the following command to view the files in the **/data** path:

      .. code-block::

         kubectl exec web-evs-auto-0 -- ls /data

      Expected output:

      .. code-block::

         lost+found

#. Run the following command to create a file named **static** in the **/data** path:

   .. code-block::

      kubectl exec web-evs-auto-0 --  touch /data/static

#. Run the following command to view the files in the **/data** path:

   .. code-block::

      kubectl exec web-evs-auto-0 -- ls /data

   Expected output:

   .. code-block::

      lost+found
      static

#. Run the following command to delete the pod named **web-evs-auto-0**:

   .. code-block::

      kubectl delete pod web-evs-auto-0

   Expected output:

   .. code-block::

      pod "web-evs-auto-0" deleted

#. After the deletion, the StatefulSet controller automatically creates a replica with the same name. Run the following command to check whether the files in the **/data** path have been modified:

   .. code-block::

      kubectl exec web-evs-auto-0 -- ls /data

   Expected output:

   .. code-block::

      lost+found
      static

   If the **static** file still exists, the data in the EVS volume can be stored persistently.

Related Operations
------------------

You can also perform the operations listed in :ref:`Table 3 <cce_10_0615__table1619535674020>`.

.. _cce_10_0615__table1619535674020:

.. table:: **Table 3** Related operations

   +---------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Operation                             | Description                                                                                                                                        | Procedure                                                                                                                                                                                |
   +=======================================+====================================================================================================================================================+==========================================================================================================================================================================================+
   | Expanding the capacity of an EVS disk | Quickly expand the capacity of a mounted EVS disk on the CCE console.                                                                              | #. Choose **Storage** in the navigation pane and click the **PersistentVolumeClaims (PVCs)** tab. Click **More** in the **Operation** column of the target PVC and select **Scale-out**. |
   |                                       |                                                                                                                                                    | #. Enter the capacity to be added and click **OK**.                                                                                                                                      |
   +---------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Viewing events                        | You can view event names, event types, number of occurrences, Kubernetes events, first occurrence time, and last occurrence time of the PVC or PV. | #. Choose **Storage** in the navigation pane and click the **PersistentVolumeClaims (PVCs)** or **PersistentVolumes (PVs)** tab.                                                         |
   |                                       |                                                                                                                                                    | #. Click **View Events** in the **Operation** column of the target PVC or PV to view events generated within one hour (event data is retained for one hour).                             |
   +---------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Viewing a YAML file                   | You can view, copy, and download the YAML files of a PVC or PV.                                                                                    | #. Choose **Storage** in the navigation pane and click the **PersistentVolumeClaims (PVCs)** or **PersistentVolumes (PVs)** tab.                                                         |
   |                                       |                                                                                                                                                    | #. Click **View YAML** in the **Operation** column of the target PVC or PV to view or download the YAML.                                                                                 |
   +---------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
