:original_name: cce_10_0630.html

.. _cce_10_0630:

Using an OBS Bucket Through a Dynamic PV
========================================

This section describes how to automatically create an OBS bucket. It is applicable when no underlying storage volume is available.

Constraints
-----------

-  If OBS volumes are used, the owner group and permission of the mount point cannot be modified.

-  CCE allows parallel file systems to be mounted using OBS SDKs or PVCs. If PVC mounting is used, the obsfs tool provided by OBS must be used. An obsfs resident process is generated each time an object storage volume generated from the parallel file system is mounted to a node, as shown in the following figure.


   .. figure:: /_static/images/en-us_image_0000001797870921.png
      :alt: **Figure 1** obsfs resident process

      **Figure 1** obsfs resident process

   Reserve 1 GiB of memory for each obsfs process. For example, for a node with 4 vCPUs and 8 GiB of memory, an obsfs parallel file system should be mounted to **no more than** eight pods.

   .. note::

      -  An obsfs resident process runs on a node. If the consumed memory exceeds the upper limit of the node, the node malfunctions. On a node with 4 vCPUs and 8 GiB of memory, if more than 100 pods are mounted to a parallel file system, the node will be unavailable. Control the number of pods mounted to a parallel file system on a single node.

-  Kata containers do not support OBS volumes.

-  OBS allows a single user to create a maximum of 100 buckets. If a large number of dynamic PVCs are created, the number of buckets may exceed the upper limit, and no more OBS buckets can be created. In this case, use OBS by calling its API or SDK and do not mount OBS buckets to workloads.

Automatically Creating an OBS Volume on the Console
---------------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Dynamically create a PVC and PV.

   a. Choose **Storage** in the navigation pane and click the **PersistentVolumeClaims (PVCs)** tab. Click **Create PVC** in the upper right corner. In the dialog box displayed, configure the PVC parameters.

      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                 |
      +===================================+=============================================================================================================================================================================================================================================================+
      | PVC Type                          | In this example, select **OBS**.                                                                                                                                                                                                                            |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PVC Name                          | Enter the PVC name, which must be unique in the same namespace.                                                                                                                                                                                             |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Creation Method                   | -  If no underlying storage is available, select **Dynamically provision** to create a PVC, PV, and underlying storage on the console in cascading mode.                                                                                                    |
      |                                   | -  If underlying storage is available, create a storage volume or use an existing storage volume to statically create a PVC based on whether a PV has been created. For details, see :ref:`Using an Existing OBS Bucket Through a Static PV <cce_10_0379>`. |
      |                                   |                                                                                                                                                                                                                                                             |
      |                                   | In this example, select **Dynamically provision**.                                                                                                                                                                                                          |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Storage Classes                   | The storage class of OBS volumes is **csi-obs**.                                                                                                                                                                                                            |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Instance Type                     | -  **Parallel file system**: a high-performance file system provided by OBS. It provides millisecond-level access latency, TB/s-level bandwidth, and million-level IOPS. **Parallel file systems are recommended.**                                         |
      |                                   | -  **Object bucket**: a container that stores objects in OBS. All objects in a bucket are at the same logical level.                                                                                                                                        |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | OBS Class                         | You can select the following object bucket types:                                                                                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                                                             |
      |                                   | -  **Standard**: Applicable when a large number of hotspot files or small-sized files need to be accessed frequently (multiple times per month on average) and require fast access response.                                                                |
      |                                   | -  **Infrequent access**: Applicable when data is not frequently accessed (fewer than 12 times per year on average) but requires fast access response.                                                                                                      |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Access Mode                       | OBS volumes support only **ReadWriteMany**, indicating that a storage volume can be mounted to multiple nodes in read/write mode. For details, see :ref:`Volume Access Modes <cce_10_0378__section43881411172418>`.                                         |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Access Key (AK/SK)                | **Custom**: Customize a secret if you want to assign different user permissions to different OBS storage devices. For details, see :ref:`Using a Custom Access Key (AK/SK) to Mount an OBS Volume <cce_10_0336>`.                                           |
      |                                   |                                                                                                                                                                                                                                                             |
      |                                   | Only secrets with the **secret.kubernetes.io/used-by = csi** label can be selected. The secret type is cfe/secure-opaque. If no secret is available, click **Create Secret** to create one.                                                                 |
      |                                   |                                                                                                                                                                                                                                                             |
      |                                   | -  **Name**: Enter a secret name.                                                                                                                                                                                                                           |
      |                                   | -  **Namespace**: Select the namespace where the secret is.                                                                                                                                                                                                 |
      |                                   | -  **Access Key (AK/SK)**: Upload a key file in .csv format. For details, see :ref:`Obtaining an Access Key <cce_10_0336__section4633162355911>`.                                                                                                           |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   b. Click **Create** to create a PVC and a PV.

      You can choose **Storage** in the navigation pane and view the created PVC and PV on the **PersistentVolumeClaims (PVCs)** and **PersistentVolumes (PVs)** tab pages, respectively.

#. Create an application.

   a. In the navigation pane on the left, click **Workloads**. In the right pane, click the **Deployments** tab.

   b. Click **Create Workload** in the upper right corner. On the displayed page, click **Data Storage** in the **Container Settings** area and click **Add Volume** to select **PVC**.

      Mount and use storage volumes, as shown in :ref:`Table 1 <cce_10_0630__cce_10_0379_table2529244345>`. For details about other parameters, see :ref:`Workloads <cce_10_0046>`.

      .. _cce_10_0630__cce_10_0379_table2529244345:

      .. table:: **Table 1** Mounting a storage volume

         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
         +===================================+=============================================================================================================================================================================================================================================================================================================================================================================================================================================================+
         | PVC                               | Select an existing object storage volume.                                                                                                                                                                                                                                                                                                                                                                                                                   |
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

      In this example, the disk is mounted to the **/data** path of the container. The container data generated in this path is stored in the OBS volume.

   c. After the configuration, click **Create Workload**.

      After the workload is created, the data in the container mount directory will be persistently stored. Verify the storage by referring to :ref:`PV Reclaim Policy <cce_10_0378__section19999142414413>`.

(kubectl) Automatically Creating an OBS Volume
----------------------------------------------

#. Use kubectl to connect to the cluster.
#. Use **StorageClass** to dynamically create a PVC and PV.

   a. Create the **pvc-obs-auto.yaml** file.

      .. code-block::

         apiVersion: v1
         kind: PersistentVolumeClaim
         metadata:
           name: pvc-obs-auto
           namespace: default
           annotations:
             everest.io/obs-volume-type: STANDARD    # Object storage type.
             csi.storage.k8s.io/fstype: obsfs        # Instance type.
             csi.storage.k8s.io/node-publish-secret-name: <your_secret_name>       # Custom secret name.
             csi.storage.k8s.io/node-publish-secret-namespace: <your_namespace>    # Namespace of the custom secret.

         spec:
           accessModes:
             - ReadWriteMany             # The value must be ReadWriteMany for object storage.
           resources:
             requests:
               storage: 1Gi               # OBS volume capacity.
           storageClassName: csi-obs    # The storage class type is OBS.

      .. table:: **Table 2** Key parameters

         +--------------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                                        | Mandatory             | Description                                                                                                                                                                                                         |
         +==================================================+=======================+=====================================================================================================================================================================================================================+
         | everest.io/obs-volume-type                       | Yes                   | OBS storage class.                                                                                                                                                                                                  |
         |                                                  |                       |                                                                                                                                                                                                                     |
         |                                                  |                       | -  If **fsType** is set to **s3fs**, **STANDARD** (standard bucket) and **WARM** (infrequent access bucket) are supported.                                                                                          |
         |                                                  |                       | -  This parameter is invalid when **fsType** is set to **obsfs**.                                                                                                                                                   |
         +--------------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | csi.storage.k8s.io/fstype                        | Yes                   | Instance type. The value can be **obsfs** or **s3fs**.                                                                                                                                                              |
         |                                                  |                       |                                                                                                                                                                                                                     |
         |                                                  |                       | -  **obsfs**: Parallel file system, which is mounted using obsfs (recommended).                                                                                                                                     |
         |                                                  |                       | -  **s3fs**: Object bucket, which is mounted using s3fs.                                                                                                                                                            |
         +--------------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | csi.storage.k8s.io/node-publish-secret-name      | No                    | Custom secret name.                                                                                                                                                                                                 |
         |                                                  |                       |                                                                                                                                                                                                                     |
         |                                                  |                       | (Recommended) Select this option if you want to assign different user permissions to different OBS storage devices. For details, see :ref:`Using a Custom Access Key (AK/SK) to Mount an OBS Volume <cce_10_0336>`. |
         +--------------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | csi.storage.k8s.io/node-publish-secret-namespace | No                    | Namespace of a custom secret.                                                                                                                                                                                       |
         +--------------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storage                                          | Yes                   | Requested capacity in the PVC, in Gi.                                                                                                                                                                               |
         |                                                  |                       |                                                                                                                                                                                                                     |
         |                                                  |                       | For OBS buckets, this field is used only for verification (cannot be empty or 0). Its value is fixed at **1**, and any value you set does not take effect for OBS buckets.                                          |
         +--------------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storageClassName                                 | Yes                   | Storage class name. The storage class name of OBS volumes is **csi-obs**.                                                                                                                                           |
         +--------------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   b. Run the following command to create a PVC:

      .. code-block::

         kubectl apply -f pvc-obs-auto.yaml

#. Create an application.

   a. Create a file named **web-demo.yaml**. In this example, the OBS volume is mounted to the **/data** path.

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
                 - name: pvc-obs-volume    #Volume name, which must be the same as the volume name in the volumes field.
                   mountPath: /data  #Location where the storage volume is mounted.
               imagePullSecrets:
                 - name: default-secret
               volumes:
                 - name: pvc-obs-volume    #Volume name, which can be customized.
                   persistentVolumeClaim:
                     claimName: pvc-obs-auto    #Name of the created PVC.

   b. Run the following command to create a workload to which the OBS volume is mounted:

      .. code-block::

         kubectl apply -f web-demo.yaml

      After the workload is created, you can try :ref:`Verifying Data Persistence and Sharing <cce_10_0630__section11593165910013>`.

.. _cce_10_0630__section11593165910013:

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

Related Operations
------------------

You can also perform the operations listed in :ref:`Table 3 <cce_10_0630__table1619535674020>`.

.. _cce_10_0630__table1619535674020:

.. table:: **Table 3** Related operations

   +------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Operation              | Description                                                                                                                                        | Procedure                                                                                                                                                                                        |
   +========================+====================================================================================================================================================+==================================================================================================================================================================================================+
   | Updating an access key | Update the access key of object storage on the CCE console.                                                                                        | #. Choose **Storage** in the navigation pane and click the **PersistentVolumeClaims (PVCs)** tab. Click **More** in the **Operation** column of the target PVC and select **Update Access Key**. |
   |                        |                                                                                                                                                    | #. Upload a key file in .csv format. For details, see :ref:`Obtaining an Access Key <cce_10_0336__section4633162355911>`. Click **OK**.                                                          |
   |                        |                                                                                                                                                    |                                                                                                                                                                                                  |
   |                        |                                                                                                                                                    |    .. note::                                                                                                                                                                                     |
   |                        |                                                                                                                                                    |                                                                                                                                                                                                  |
   |                        |                                                                                                                                                    |       After a global access key is updated, all pods mounted with the object storage that uses this access key can be accessed only after being restarted.                                       |
   +------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Viewing events         | You can view event names, event types, number of occurrences, Kubernetes events, first occurrence time, and last occurrence time of the PVC or PV. | #. Choose **Storage** in the navigation pane and click the **PersistentVolumeClaims (PVCs)** or **PersistentVolumes (PVs)** tab.                                                                 |
   |                        |                                                                                                                                                    | #. Click **View Events** in the **Operation** column of the target PVC or PV to view events generated within one hour (event data is retained for one hour).                                     |
   +------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Viewing a YAML file    | You can view, copy, and download the YAML files of a PVC or PV.                                                                                    | #. Choose **Storage** in the navigation pane and click the **PersistentVolumeClaims (PVCs)** or **PersistentVolumes (PVs)** tab.                                                                 |
   |                        |                                                                                                                                                    | #. Click **View YAML** in the **Operation** column of the target PVC or PV to view or download the YAML.                                                                                         |
   +------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
