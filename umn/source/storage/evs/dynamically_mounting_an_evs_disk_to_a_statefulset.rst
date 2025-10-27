:original_name: cce_10_0616.html

.. _cce_10_0616:

Dynamically Mounting an EVS Disk to a StatefulSet
=================================================

Application Scenarios
---------------------

Dynamic mounting is available only for creating a :ref:`StatefulSet <cce_10_0048>`. It is implemented through a volume claim template (`volumeClaimTemplates <https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/#volume-claim-templates>`__ field) and depends on dynamic creation of PVs through StorageClass. In this mode, each pod in a multi-pod StatefulSet is associated with a unique PVC and PV. After a pod is rescheduled, the original data can still be mounted to it based on the PVC name. In the common mounting mode for a Deployment, if ReadWriteMany is supported, multiple pods of the Deployment will be mounted to the same underlying storage.

.. note::

   When updating a StatefulSet in Kubernetes, it is not allowed to add or delete the **volumeClaimTemplates** field. This field can only be configured during the creation of the StatefulSet.

Prerequisites
-------------

-  You have created a cluster and installed the :ref:`CCE Container Storage (Everest) <cce_10_0066>` add-on in the cluster.
-  To create a cluster using commands, ensure kubectl is used. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

Dynamically Mounting an EVS Disk Using the Console
--------------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. Choose **Workloads** in the navigation pane. In the right pane, click the **StatefulSets** tab.

#. Click **Create Workload** in the upper right corner. On the displayed page, click **Data Storage** in the **Container Information** area under **Container Settings** and choose **Add Volume** > **VCT**.

#. Click **Create PVC**. In the dialog box displayed, configure PVC parameters.

   Click **Create**.

   +---------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                             | Description                                                                                                                                                                                                                                                                                                |
   +=======================================+============================================================================================================================================================================================================================================================================================================+
   | PVC Type                              | In this example, select **EVS**.                                                                                                                                                                                                                                                                           |
   +---------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | PVC Name                              | Enter the name of the PVC. After a PVC is created, a suffix is automatically added based on the number of pods. The format is <*Custom PVC name*>-<*Serial number*>, for example, example-0.                                                                                                               |
   +---------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Creation Method                       | You can select **Dynamically provision** to create a PVC, PV, and underlying storage on the console in cascading mode.                                                                                                                                                                                     |
   +---------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Storage Classes                       | The default StorageClasses for EVS disks are **csi-disk** and **csi-disk-topology**.                                                                                                                                                                                                                       |
   |                                       |                                                                                                                                                                                                                                                                                                            |
   |                                       | .. note::                                                                                                                                                                                                                                                                                                  |
   |                                       |                                                                                                                                                                                                                                                                                                            |
   |                                       |    If you use the **csi-disk** (EVS) StorageClass, a PVC and PV will be created immediately. The EVS disk is created with the PV, and then the PVC is bound to the PV.                                                                                                                                     |
   |                                       |                                                                                                                                                                                                                                                                                                            |
   |                                       |    If you use the **csi-disk-topology** (EVS created with a delay) StorageClass, a PV will not be immediately created when a PVC is created. Instead, the pods that will be associated with the PVC are scheduled first, and then the EVS disk and PV are created, and finally the PV is bound to the PVC. |
   |                                       |                                                                                                                                                                                                                                                                                                            |
   |                                       | You can customize a StorageClass and configure its reclaim policy and binding mode. For details, see :ref:`Creating a StorageClass Through the Console <cce_10_0380__section1074117311660>`.                                                                                                               |
   +---------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | (Optional) Storage Volume Name Prefix | Available only when the cluster version is v1.23.14-r0, v1.25.9-r0, v1.27.6-r0, v1.28.4-r0, or later, and Everest of v2.4.15 or later is installed in the cluster.                                                                                                                                         |
   |                                       |                                                                                                                                                                                                                                                                                                            |
   |                                       | This parameter specifies the name of the underlying storage that is automatically created. The actual underlying storage name is in the format of "Storage volume name prefix + PVC UID". If this parameter is left blank, the default prefix **pvc** will be used.                                        |
   |                                       |                                                                                                                                                                                                                                                                                                            |
   |                                       | For example, if the storage volume name prefix is set to **test**, the actual underlying storage name is **test-**\ *{UID}*.                                                                                                                                                                               |
   +---------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | AZ                                    | Select the AZ of the EVS disk. The AZ must be the same as that of the cluster node.                                                                                                                                                                                                                        |
   |                                       |                                                                                                                                                                                                                                                                                                            |
   |                                       | .. note::                                                                                                                                                                                                                                                                                                  |
   |                                       |                                                                                                                                                                                                                                                                                                            |
   |                                       |    An EVS disk can only be mounted to a node in the same AZ. After an EVS disk is created, its AZ cannot be changed.                                                                                                                                                                                       |
   +---------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Disk Type                             | Select an EVS disk type. EVS disk types vary depending on regions. Obtain the available EVS types on the console.                                                                                                                                                                                          |
   |                                       |                                                                                                                                                                                                                                                                                                            |
   |                                       | .. note::                                                                                                                                                                                                                                                                                                  |
   |                                       |                                                                                                                                                                                                                                                                                                            |
   |                                       |    If the Everest version is 2.4.4 or later, general-purpose SSD V2 EVS disks are supported. General-purpose SSD V2 disks allow you to specify the disk IOPS and throughput.                                                                                                                               |
   +---------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Capacity (GiB)                        | Capacity of the requested storage volume.                                                                                                                                                                                                                                                                  |
   +---------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Access Mode                           | EVS volumes support only **ReadWriteOnce**, indicating that a storage volume can be mounted to one node in read/write mode. For details, see :ref:`Volume Access Modes <cce_10_0378__section43881411172418>`.                                                                                              |
   +---------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Encryption                            | Configure whether to encrypt underlying storage. If you select **Enabled (key)**, an encryption key must be configured. Before using encryption, check whether the region where the EVS disk is located supports disk encryption.                                                                          |
   +---------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Resource Tag                          | You can add resource tags to classify resources, which is supported only when the Everest version in the cluster is 2.1.39 or later.                                                                                                                                                                       |
   |                                       |                                                                                                                                                                                                                                                                                                            |
   |                                       | You can create **predefined tags** on the TMS console. These tags are available to all resources that support tags. You can use these tags to improve the tag creation and resource migration efficiency.                                                                                                  |
   |                                       |                                                                                                                                                                                                                                                                                                            |
   |                                       | CCE automatically creates system tags **CCE-Cluster-ID=**\ *{Cluster ID}*, **CCE-Cluster-Name=**\ *{Cluster name}*, and **CCE-Namespace=**\ *{Namespace name}*. These tags cannot be modified.                                                                                                             |
   |                                       |                                                                                                                                                                                                                                                                                                            |
   |                                       | .. note::                                                                                                                                                                                                                                                                                                  |
   |                                       |                                                                                                                                                                                                                                                                                                            |
   |                                       |    After a dynamic PV of the EVS type is created, the resource tags cannot be updated on the CCE console. To update these resource tags, go to the EVS console.                                                                                                                                            |
   +---------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Enter the path to which the volume is mounted.

   .. table:: **Table 1** Mounting a storage volume

      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                        |
      +===================================+====================================================================================================================================================================================================================================================================================================================================================================================================================================================+
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

#. Dynamically mount and use storage volumes. For details about other parameters, see :ref:`Creating a StatefulSet <cce_10_0048>`. After the configuration, click **Create Workload**.

   After the workload is created, the data in the container mount directory will be persistently stored. Verify the storage by referring to :ref:`Verifying Data Persistence <cce_10_0616__section11593165910013>`.

Dynamically Mounting an EVS Disk Using kubectl
----------------------------------------------

#. Use kubectl to access the cluster.

#. Create a file named **statefulset-evs.yaml**. In this example, the EVS volume is mounted to the **/data** path.

   .. code-block::

      apiVersion: apps/v1
      kind: StatefulSet
      metadata:
        name: statefulset-evs
        namespace: default
      spec:
        selector:
          matchLabels:
            app: statefulset-evs
        template:
          metadata:
            labels:
              app: statefulset-evs
          spec:
            containers:
              - name: container-1
                image: nginx:latest
                volumeMounts:
                  - name: pvc-disk           # The value must be the same as that in the volumeClaimTemplates field.
                    mountPath: /data         # Location where the storage volume is mounted
            imagePullSecrets:
              - name: default-secret
        serviceName: statefulset-evs         # Headless Service name
        replicas: 2
        volumeClaimTemplates:
          - apiVersion: v1
            kind: PersistentVolumeClaim
            metadata:
              name: pvc-disk
              namespace: default
              annotations:
                everest.io/disk-volume-type: SAS    # EVS disk type
                everest.io/crypt-key-id: <your_key_id>    # (Optional) Encryption key ID. Mandatory for an encrypted disk.
                everest.io/disk-volume-tags: '{"key1":"value1","key2":"value2"}' # (Optional) Custom resource tags
                csi.storage.k8s.io/fstype: xfs    # (Optional) The file system is of the xfs type. If it is left blank, ext4 will be used by default.
                everest.io/disk-iops: '3000'      # (Optional) IOPS of only a GPSSD2 EVS disk
                everest.io/disk-throughput: '125' # (Optional) Throughput of only a GPSSD2 EVS disk
                everest.io/csi.volume-name-prefix: test  # (Optional) Storage volume name prefix of the automatically created underlying storage
              labels:
                failure-domain.beta.kubernetes.io/region: <your_region>   # Region of the node where the application is to be deployed
                failure-domain.beta.kubernetes.io/zone: <your_zone>       # AZ of the node where the application is to be deployed
            spec:
              accessModes:
                - ReadWriteOnce               # The value must be ReadWriteOnce for EVS disks.
              resources:
                requests:
                  storage: 10Gi             # EVS disk capacity, ranging from 1 to 32768
              storageClassName: csi-disk    # The StorageClass is EVS.
      ---
      apiVersion: v1
      kind: Service
      metadata:
        name: statefulset-evs   # Headless Service name
        namespace: default
        labels:
          app: statefulset-evs
      spec:
        selector:
          app: statefulset-evs
        clusterIP: None
        ports:
          - name: statefulset-evs
            targetPort: 80
            nodePort: 0
            port: 80
            protocol: TCP
        type: ClusterIP

   .. table:: **Table 2** Key parameters

      +------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                | Mandatory             | Description                                                                                                                                                                                                                                                                                                              |
      +==========================================+=======================+==========================================================================================================================================================================================================================================================================================================================+
      | failure-domain.beta.kubernetes.io/region | Yes                   | Region where the cluster is located.                                                                                                                                                                                                                                                                                     |
      |                                          |                       |                                                                                                                                                                                                                                                                                                                          |
      |                                          |                       | For details about its value, see `Regions and Endpoints <https://docs.otc.t-systems.com/regions-and-endpoints/index.html>`__.                                                                                                                                                                                            |
      +------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | failure-domain.beta.kubernetes.io/zone   | Yes                   | AZ where the EVS volume is created. It must be the same as the AZ planned for the workload.                                                                                                                                                                                                                              |
      |                                          |                       |                                                                                                                                                                                                                                                                                                                          |
      |                                          |                       | For details about its value, see `Regions and Endpoints <https://docs.otc.t-systems.com/regions-and-endpoints/index.html>`__.                                                                                                                                                                                            |
      +------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | everest.io/disk-volume-type              | Yes                   | EVS disk type. All letters are in uppercase.                                                                                                                                                                                                                                                                             |
      |                                          |                       |                                                                                                                                                                                                                                                                                                                          |
      |                                          |                       | -  **SATA**: common I/O                                                                                                                                                                                                                                                                                                  |
      |                                          |                       | -  **SAS**: high I/O                                                                                                                                                                                                                                                                                                     |
      |                                          |                       | -  **SSD**: ultra-high I/O                                                                                                                                                                                                                                                                                               |
      |                                          |                       | -  **GPSSD**: general-purpose SSD                                                                                                                                                                                                                                                                                        |
      |                                          |                       | -  **ESSD**: extreme SSD                                                                                                                                                                                                                                                                                                 |
      |                                          |                       | -  **GPSSD2**: general-purpose SSD v2, which is supported when the Everest version is 2.4.4 or later and the **everest.io/disk-iops** and **everest.io/disk-throughput** annotations are configured                                                                                                                      |
      +------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | everest.io/disk-iops                     | No                    | Preconfigured IOPS, which is supported only by general-purpose SSD v2 EVS disks.                                                                                                                                                                                                                                         |
      |                                          |                       |                                                                                                                                                                                                                                                                                                                          |
      |                                          |                       | -  The IOPS of general-purpose SSD v2 EVS disks ranges from 3000 to 128000, and the maximum value is 500 times of the capacity (GiB).                                                                                                                                                                                    |
      |                                          |                       |                                                                                                                                                                                                                                                                                                                          |
      |                                          |                       |    If the IOPS of general-purpose SSD v2 disks is greater than 3000, extra IOPS will be billed.                                                                                                                                                                                                                          |
      +------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | everest.io/disk-throughput               | No                    | Preconfigured throughput, which is supported only by general-purpose SSD v2 EVS disks.                                                                                                                                                                                                                                   |
      |                                          |                       |                                                                                                                                                                                                                                                                                                                          |
      |                                          |                       | The value ranges from 125 MiB/s to 1000 MiB/s. The maximum value is a quarter of IOPS.                                                                                                                                                                                                                                   |
      |                                          |                       |                                                                                                                                                                                                                                                                                                                          |
      |                                          |                       | If the throughput is greater than 125 MiB/s, extra throughput will be billed. For details, see .                                                                                                                                                                                                                         |
      +------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | everest.io/crypt-key-id                  | No                    | Mandatory when the EVS disk is encrypted. Enter the encryption key ID selected during EVS disk creation.                                                                                                                                                                                                                 |
      |                                          |                       |                                                                                                                                                                                                                                                                                                                          |
      |                                          |                       | To obtain an encryption key ID, log in to the **Cloud Server Console**. In the navigation pane, choose **Elastic Volume Service** > **Disks**. Click the name of the target EVS disk to go to its details page. On the **Summary** tab page, copy the value of **KMS Key ID** in the **Configuration Information** area. |
      +------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | everest.io/disk-volume-tags              | No                    | (Optional) This parameter is supported when the Everest version in the cluster is 2.1.39 or later.                                                                                                                                                                                                                       |
      |                                          |                       |                                                                                                                                                                                                                                                                                                                          |
      |                                          |                       | You can add resource tags to classify resources.                                                                                                                                                                                                                                                                         |
      |                                          |                       |                                                                                                                                                                                                                                                                                                                          |
      |                                          |                       | You can create **predefined tags** on the TMS console. These tags are available to all resources that support tags. You can use these tags to improve the tag creation and resource migration efficiency.                                                                                                                |
      |                                          |                       |                                                                                                                                                                                                                                                                                                                          |
      |                                          |                       | CCE automatically creates system tags **CCE-Cluster-ID=**\ *{Cluster ID}*, **CCE-Cluster-Name=**\ *{Cluster name}*, and **CCE-Namespace=**\ *{Namespace name}*. These tags cannot be modified.                                                                                                                           |
      +------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | csi.storage.k8s.io/fstype                | No                    | (Optional) This parameter specifies the file system type, which defaults to **ext4**.                                                                                                                                                                                                                                    |
      |                                          |                       |                                                                                                                                                                                                                                                                                                                          |
      |                                          |                       | The value can be **ext4** or **xfs**. The restrictions on using **xfs** are as follows:                                                                                                                                                                                                                                  |
      |                                          |                       |                                                                                                                                                                                                                                                                                                                          |
      |                                          |                       | -  The nodes must run CentOS 7, HCE OS 2.0, or Ubuntu 22.04, and the Everest version in the cluster must be 2.3.2 or later.                                                                                                                                                                                              |
      |                                          |                       | -  Only common containers are supported.                                                                                                                                                                                                                                                                                 |
      +------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | everest.io/csi.volume-name-prefix        | No                    | (Optional) This parameter is available only when the cluster version is v1.23.14-r0, v1.25.9-r0, v1.27.6-r0, v1.28.4-r0, or later, and Everest of v2.4.15 or later is installed in the cluster.                                                                                                                          |
      |                                          |                       |                                                                                                                                                                                                                                                                                                                          |
      |                                          |                       | This parameter specifies the name of the underlying storage that is automatically created. The actual underlying storage name is in the format of "Storage volume name prefix + PVC UID". If this parameter is left blank, the default prefix **pvc** will be used.                                                      |
      |                                          |                       |                                                                                                                                                                                                                                                                                                                          |
      |                                          |                       | Enter 1 to 26 characters that cannot start or end with a hyphen (-). Only lowercase letters, digits, and hyphens (-) are allowed.                                                                                                                                                                                        |
      |                                          |                       |                                                                                                                                                                                                                                                                                                                          |
      |                                          |                       | For example, if the storage volume name prefix is set to **test**, the actual underlying storage name is **test-**\ *{UID}*.                                                                                                                                                                                             |
      +------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | storage                                  | Yes                   | Requested PVC capacity, in Gi. The value is an integer ranging from **1** to **32768**.                                                                                                                                                                                                                                  |
      |                                          |                       |                                                                                                                                                                                                                                                                                                                          |
      |                                          |                       | If **storage** is set to a decimal, the value will be rounded up for the EVS disk. For example, if **storage** is set to **10.1Gi**, an 11-GiB EVS disk will be created.                                                                                                                                                 |
      +------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | storageClassName                         | Yes                   | StorageClass name, which is **csi-disk** for an EVS disk.                                                                                                                                                                                                                                                                |
      +------------------------------------------+-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Run the following command to create a workload to which the EVS volume is mounted:

   .. code-block::

      kubectl apply -f statefulset-evs.yaml

   After the workload is created, the data in the container mount directory will be persistently stored. Verify the storage by referring to :ref:`Verifying Data Persistence <cce_10_0616__section11593165910013>`.

.. _cce_10_0616__section11593165910013:

Verifying Data Persistence
--------------------------

#. View the deployed application and EVS volume files.

   a. Run the following command to view the created pod:

      .. code-block::

         kubectl get pod | grep statefulset-evs

      Expected output:

      .. code-block::

         statefulset-evs-0          1/1     Running   0             45s
         statefulset-evs-1          1/1     Running   0             28s

   b. Run the following command to check whether the EVS volume has been mounted to the **/data** path:

      .. code-block::

         kubectl exec statefulset-evs-0 -- df | grep data

      Expected output:

      .. code-block::

         /dev/sdd              10255636     36888  10202364   0% /data

   c. Run the following command to check the files in the **/data** path:

      .. code-block::

         kubectl exec statefulset-evs-0 -- ls /data

      Expected output:

      .. code-block::

         lost+found

#. Run the following command to create a file named **static** in the **/data** path:

   .. code-block::

      kubectl exec statefulset-evs-0 --  touch /data/static

#. Run the following command to check the files in the **/data** path:

   .. code-block::

      kubectl exec statefulset-evs-0 -- ls /data

   Expected output:

   .. code-block::

      lost+found
      static

#. Run the following command to delete the pod named **statefulset-evs-0**:

   .. code-block::

      kubectl delete pod statefulset-evs-0

   Expected output:

   .. code-block::

      pod "statefulset-evs-0" deleted

#. After the deletion, the StatefulSet controller automatically creates a replica with the same name. Run the following command to check whether the files in the **/data** path have been modified:

   .. code-block::

      kubectl exec statefulset-evs-0 -- ls /data

   Expected output:

   .. code-block::

      lost+found
      static

   The **static** file is retained, indicating that the data in the EVS volume can be stored persistently.

Related Operations
------------------

You can also perform the operations listed in :ref:`Table 3 <cce_10_0616__cce_10_0615_table1619535674020>`.

.. _cce_10_0616__cce_10_0615_table1619535674020:

.. table:: **Table 3** Related operations

   +---------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Operation                             | Description                                                                                                                                | Procedure                                                                                                                                                                       |
   +=======================================+============================================================================================================================================+=================================================================================================================================================================================+
   | Expanding the capacity of an EVS disk | Quickly expand the capacity of an attached EVS disk on the CCE console.                                                                    | #. Choose **Storage** in the navigation pane. In the right pane, click the **PVCs** tab. Click **More** in the **Operation** column of the target PVC and select **Scale-out**. |
   |                                       |                                                                                                                                            | #. Enter the capacity to be added and click **OK**.                                                                                                                             |
   +---------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Viewing events                        | View event names, event types, number of occurrences, Kubernetes events, first occurrence time, and last occurrence time of the PVC or PV. | #. Choose **Storage** in the navigation pane. In the right pane, click the **PVCs** or **PVs** tab.                                                                             |
   |                                       |                                                                                                                                            | #. Click **View Events** in the **Operation** column of the target PVC or PV to view events generated within one hour (events are retained for one hour).                       |
   +---------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Viewing a YAML file                   | View, copy, or download the YAML file of a PVC or PV.                                                                                      | #. Choose **Storage** in the navigation pane. In the right pane, click the **PVCs** or **PVs** tab.                                                                             |
   |                                       |                                                                                                                                            | #. Click **View YAML** in the **Operation** column of the target PVC or PV to view or download the YAML.                                                                        |
   +---------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
