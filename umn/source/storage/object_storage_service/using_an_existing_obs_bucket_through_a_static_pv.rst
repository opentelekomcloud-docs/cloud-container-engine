:original_name: cce_10_0379.html

.. _cce_10_0379:

Using an Existing OBS Bucket Through a Static PV
================================================

This section describes how to use an existing Object Storage Service (OBS) bucket to statically create PVs and PVCs for data persistence and sharing in workloads.

Prerequisites
-------------

-  You have created a cluster and installed the :ref:`CCE Container Storage (Everest) <cce_10_0066>` add-on in the cluster.
-  To create a cluster using commands, ensure kubectl is used. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

Notes and Constraints
---------------------

-  If OBS volumes are used, the owner group and permission of the mount point cannot be modified.
-  Every time an OBS volume is mounted to a workload through a PVC, a resident process is created in the backend. When a workload uses too many OBS volumes or reads and writes a large number of object storage files, resident processes will consume a significant amount of memory. To ensure stable running of the workload, make sure that the number of OBS volumes used does not exceed the requested memory. For example, if the workload requests for 4 GiB of memory, the number of OBS volumes should be **no more than** 4.
-  Kata containers do not support OBS volumes.
-  Hard links are not supported when common buckets are mounted.

-  Multiple PVs can use the same OBS storage volume with the following restrictions:

   -  Do not mount the PVCs/PVs that use the same underlying OBS volume to one pod. This will lead to a pod startup failure because not all PVCs can be mounted to the pod due to the same **volumeHandle** value.
   -  The **persistentVolumeReclaimPolicy** parameter in the PVs must be set to **Retain**. Otherwise, when a PV is deleted, the associated underlying volume may be deleted. In this case, other PVs associated with the underlying volume malfunction.
   -  If underlying storage is repeatedly used, you are required to maintain data consistency. Enable isolation and protection for ReadWriteMany at the application layer and prevent multiple clients from writing the same file to prevent data overwriting and loss.

Using an Existing OBS Bucket on the Console
-------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Statically create a PVC and PV.

   a. Choose **Storage** in the navigation pane. In the right pane, click the **PVCs** tab. Click **Create PVC** in the upper right corner. In the dialog box displayed, configure PVC parameters.

      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                     |
      +===================================+=================================================================================================================================================================================================================================+
      | PVC Type                          | In this example, select **OBS**.                                                                                                                                                                                                |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PVC Name                          | Enter the PVC name, which must be unique in a namespace.                                                                                                                                                                        |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Creation Method                   | -  If underlying storage is available, create a PV or use an existing PV to statically create a PVC.                                                                                                                            |
      |                                   | -  If no underlying storage is available, select **Dynamically provision**. For details, see :ref:`Using an OBS Bucket Through a Dynamic PV <cce_10_0630>`.                                                                     |
      |                                   |                                                                                                                                                                                                                                 |
      |                                   | In this example, select **Create new** to create both a PV and PVC on the console.                                                                                                                                              |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PV\ :sup:`a`                      | Select an existing PV in the cluster. For details about how to create a PV, see "Creating a storage volume" in :ref:`Related Operations <cce_10_0379__section16505832153318>`.                                                  |
      |                                   |                                                                                                                                                                                                                                 |
      |                                   | You do not need to specify this parameter in this example.                                                                                                                                                                      |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | OBS\ :sup:`b`                     | Click **Select OBS**. On the displayed page, select the OBS volume that meets your requirements and click **OK**.                                                                                                               |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PV Name\ :sup:`b`                 | Enter the PV name, which must be unique in the same cluster.                                                                                                                                                                    |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Access Mode\ :sup:`b`             | OBS volumes support only **ReadWriteMany**, indicating that a storage volume can be mounted to multiple nodes in read/write mode. For details, see :ref:`Volume Access Modes <cce_10_0378__section43881411172418>`.             |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Reclaim Policy\ :sup:`b`          | You can select **Delete** or **Retain** to specify the reclaim policy of the underlying storage when the PVC is deleted. For details, see :ref:`PV Reclaim Policy <cce_10_0378__section19999142414413>`.                        |
      |                                   |                                                                                                                                                                                                                                 |
      |                                   | .. note::                                                                                                                                                                                                                       |
      |                                   |                                                                                                                                                                                                                                 |
      |                                   |    If multiple PVs use the same OBS volume, use **Retain** to prevent the underlying volume from being deleted with a PV.                                                                                                       |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Access Key (AK/SK)\ :sup:`b`      | **Custom** (Recommended): Customize a secret if you want to assign different user permissions to different OBS storage devices. For details, see :ref:`Using a Custom Access Key (AK/SK) to Mount an OBS Volume <cce_10_0336>`. |
      |                                   |                                                                                                                                                                                                                                 |
      |                                   | Only secrets with the **secret.kubernetes.io/used-by = csi** label can be selected. The secret type is cfe/secure-opaque. If no secret is available, click **Create Secret** to create one.                                     |
      |                                   |                                                                                                                                                                                                                                 |
      |                                   | -  **Name**: Enter a secret name.                                                                                                                                                                                               |
      |                                   | -  **Namespace**: Select the namespace where the secret is.                                                                                                                                                                     |
      |                                   | -  **Access Key (AK/SK)**: Upload a key file in .csv format. For details, see :ref:`Obtaining an Access Key <cce_10_0336__section4633162355911>`.                                                                               |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Mount Options\ :sup:`b`           | Enter the mounting parameter key-value pairs. For details, see :ref:`Configuring OBS Mount Options <cce_10_0631>`.                                                                                                              |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

      .. note::

         a: The parameter is available when **Creation Method** is set to **Use existing**.

         b: The parameter is available when **Creation Method** is set to **Create new**.

   b. Click **Create** to create a PVC and a PV.

      You can choose **Storage** in the navigation pane and view the created PVC and PV on the **PVCs** and **PVs** tab pages, respectively.

#. Create an application.

   a. Choose **Workloads** in the navigation pane. In the right pane, click the **Deployments** tab.

   b. Click **Create Workload** in the upper right corner. On the displayed page, click **Data Storage** in the **Container Settings** area and click **Add Volume** to select **PVC**.

      Mount and use storage volumes, as shown in :ref:`Table 1 <cce_10_0379__table2529244345>`. For details about other parameters, see :ref:`Workloads <cce_10_0046>`.

      .. _cce_10_0379__table2529244345:

      .. table:: **Table 1** Mounting a storage volume

         +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                        |
         +===================================+====================================================================================================================================================================================================================================================================================================================================================================================================================================================+
         | PVC                               | Select an existing OBS volume.                                                                                                                                                                                                                                                                                                                                                                                                                     |
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

      In this example, the disk is mounted to the **/data** path of the container. The container data generated in this path is stored in the OBS volume.

   c. After the configuration, click **Create Workload**.

      After the workload is created, the data in the container mount directory will be persistently stored. Verify the storage by referring to :ref:`Verifying Data Persistence and Sharing <cce_10_0379__section11593165910013>`.

Using an Existing OBS Bucket Through kubectl
--------------------------------------------

#. Use kubectl to access the cluster.
#. Create a PV.

   a. .. _cce_10_0379__li162841212145314:

      Create the **pv-obs.yaml** file.

      .. code-block::

         apiVersion: v1
         kind: PersistentVolume
         metadata:
           annotations:
             pv.kubernetes.io/provisioned-by: everest-csi-provisioner
             everest.io/reclaim-policy: retain-volume-only      # (Optional) The underlying volume is retained when the PV is deleted.
           name: pv-obs       # PV name
         spec:
           accessModes:
           - ReadWriteMany    # Access mode. The value must be ReadWriteMany for OBS.
           capacity:
             storage: 1Gi     # OBS volume capacity
           csi:
             driver: obs.csi.everest.io        # Dependent storage driver for the mounting
             fsType: obsfs                     # Instance type
             volumeHandle: <your_volume_id>    # Name of the OBS volume
             volumeAttributes:
               storage.kubernetes.io/csiProvisionerIdentity: everest-csi-provisioner
               everest.io/obs-volume-type: STANDARD
               everest.io/region: <your_region>                        # Region where the OBS volume is

             nodePublishSecretRef:            # Custom secret of the OBS volume
               name: <your_secret_name>       # Custom secret name
               namespace: <your_namespace>    # Namespace of the custom secret
           persistentVolumeReclaimPolicy: Retain    # Reclaim policy
           storageClassName: csi-obs               # StorageClass name
           mountOptions: []                         # Mount options

      .. table:: **Table 2** Key parameters

         +-----------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                                     | Mandatory             | Description                                                                                                                                                                                                                                                                                             |
         +===============================================+=======================+=========================================================================================================================================================================================================================================================================================================+
         | everest.io/reclaim-policy: retain-volume-only | No                    | Optional.                                                                                                                                                                                                                                                                                               |
         |                                               |                       |                                                                                                                                                                                                                                                                                                         |
         |                                               |                       | Only **retain-volume-only** is supported.                                                                                                                                                                                                                                                               |
         |                                               |                       |                                                                                                                                                                                                                                                                                                         |
         |                                               |                       | This parameter is valid only when the Everest version is 1.2.9 or later and the reclaim policy is **Delete**. If the reclaim policy is **Delete** and the current value is **retain-volume-only**, the associated PV is deleted while the underlying storage volume is retained, when a PVC is deleted. |
         +-----------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | fsType                                        | Yes                   | Instance type. The value can be **obsfs** or **s3fs**.                                                                                                                                                                                                                                                  |
         |                                               |                       |                                                                                                                                                                                                                                                                                                         |
         |                                               |                       | -  **obsfs**: a parallel file system                                                                                                                                                                                                                                                                    |
         |                                               |                       | -  **s3fs**: object bucket                                                                                                                                                                                                                                                                              |
         +-----------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | volumeHandle                                  | Yes                   | OBS volume name.                                                                                                                                                                                                                                                                                        |
         +-----------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | everest.io/obs-volume-type                    | Yes                   | OBS storage class.                                                                                                                                                                                                                                                                                      |
         |                                               |                       |                                                                                                                                                                                                                                                                                                         |
         |                                               |                       | -  If **fsType** is set to **s3fs**, **STANDARD** (standard bucket) and **WARM** (infrequent access bucket) are supported.                                                                                                                                                                              |
         |                                               |                       | -  This parameter is invalid when **fsType** is set to **obsfs**.                                                                                                                                                                                                                                       |
         +-----------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | everest.io/region                             | Yes                   | Region where the OBS bucket is deployed.                                                                                                                                                                                                                                                                |
         |                                               |                       |                                                                                                                                                                                                                                                                                                         |
         |                                               |                       | For details about its value, see `Regions and Endpoints <https://docs.otc.t-systems.com/regions-and-endpoints/index.html>`__.                                                                                                                                                                           |
         +-----------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | nodePublishSecretRef                          | No                    | Access key (AK/SK) used for mounting the object storage volume. You can use the AK/SK to create a secret and mount it to the PV. For details, see :ref:`Using a Custom Access Key (AK/SK) to Mount an OBS Volume <cce_10_0336>`.                                                                        |
         |                                               |                       |                                                                                                                                                                                                                                                                                                         |
         |                                               |                       | An example is as follows:                                                                                                                                                                                                                                                                               |
         |                                               |                       |                                                                                                                                                                                                                                                                                                         |
         |                                               |                       | .. code-block::                                                                                                                                                                                                                                                                                         |
         |                                               |                       |                                                                                                                                                                                                                                                                                                         |
         |                                               |                       |    nodePublishSecretRef:                                                                                                                                                                                                                                                                                |
         |                                               |                       |      name: secret-demo                                                                                                                                                                                                                                                                                  |
         |                                               |                       |      namespace: default                                                                                                                                                                                                                                                                                 |
         +-----------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | mountOptions                                  | No                    | Mount options. For details, see :ref:`Configuring OBS Mount Options <cce_10_0631>`.                                                                                                                                                                                                                     |
         +-----------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | persistentVolumeReclaimPolicy                 | Yes                   | A reclaim policy is supported when the cluster version is or later than 1.19.10 and the Everest version is or later than 1.2.9.                                                                                                                                                                         |
         |                                               |                       |                                                                                                                                                                                                                                                                                                         |
         |                                               |                       | The **Delete** and **Retain** reclaim policies are supported. For details, see :ref:`PV Reclaim Policy <cce_10_0378__section19999142414413>`. If multiple PVs use the same OBS volume, use **Retain** to prevent the underlying volume from being deleted with a PV.                                    |
         |                                               |                       |                                                                                                                                                                                                                                                                                                         |
         |                                               |                       | **Delete**:                                                                                                                                                                                                                                                                                             |
         |                                               |                       |                                                                                                                                                                                                                                                                                                         |
         |                                               |                       | -  If **everest.io/reclaim-policy** is not specified, both the PV and storage resources will be deleted when a PVC is deleted.                                                                                                                                                                          |
         |                                               |                       | -  If **everest.io/reclaim-policy** is set to **retain-volume-only**, when a PVC is deleted, the PV will be deleted but the storage resources will be retained.                                                                                                                                         |
         |                                               |                       |                                                                                                                                                                                                                                                                                                         |
         |                                               |                       | **Retain**: When a PVC is deleted, both the PV and underlying storage resources will be retained. You need to manually delete these resources. After the PVC is deleted, the PV is in the **Released** state and cannot be bound to a PVC again.                                                        |
         +-----------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storage                                       | Yes                   | Storage capacity, in Gi.                                                                                                                                                                                                                                                                                |
         |                                               |                       |                                                                                                                                                                                                                                                                                                         |
         |                                               |                       | For OBS, this field is used only for verification (cannot be empty or 0). Its value is fixed at **1**, and any value you set does not take effect for OBS.                                                                                                                                              |
         +-----------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storageClassName                              | Yes                   | StorageClass name, which is **csi-obs** for an OBS volume.                                                                                                                                                                                                                                              |
         +-----------------------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   b. Run the following command to create a PV:

      .. code-block::

         kubectl apply -f pv-obs.yaml

#. Create a PVC.

   a. Create the **pvc-obs.yaml** file.

      .. code-block::

         apiVersion: v1
         kind: PersistentVolumeClaim
         metadata:
           name: pvc-obs
           namespace: default
           annotations:
             volume.beta.kubernetes.io/storage-provisioner: everest-csi-provisioner
             everest.io/obs-volume-type: STANDARD
             csi.storage.k8s.io/fstype: obsfs
             csi.storage.k8s.io/node-publish-secret-name: <your_secret_name>  # Custom secret name.
             csi.storage.k8s.io/node-publish-secret-namespace: <your_namespace>        # Namespace of the custom secret.

         spec:
           accessModes:
           - ReadWriteMany                  # The value must be ReadWriteMany for OBS.
           resources:
             requests:
               storage: 1Gi
           storageClassName: csi-obs       # StorageClass name, which must be the same as that of the PV
           volumeName: pv-obs    # PV name

      .. table:: **Table 3** Key parameters

         +--------------------------------------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                                        | Mandatory             | Description                                                                                                                                                |
         +==================================================+=======================+============================================================================================================================================================+
         | csi.storage.k8s.io/node-publish-secret-name      | No                    | Name of the custom secret specified in the PV.                                                                                                             |
         +--------------------------------------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | csi.storage.k8s.io/node-publish-secret-namespace | No                    | Namespace of the custom secret specified in the PV.                                                                                                        |
         +--------------------------------------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storage                                          | Yes                   | Requested capacity in the PVC, in Gi.                                                                                                                      |
         |                                                  |                       |                                                                                                                                                            |
         |                                                  |                       | For OBS, this field is used only for verification (cannot be empty or 0). Its value is fixed at **1**, and any value you set does not take effect for OBS. |
         +--------------------------------------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storageClassName                                 | Yes                   | StorageClass name, which must be the same as the StorageClass of the PV in :ref:`1 <cce_10_0379__li162841212145314>`.                                      |
         |                                                  |                       |                                                                                                                                                            |
         |                                                  |                       | StorageClass name, which is **csi-obs** for an OBS volume.                                                                                                 |
         +--------------------------------------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | volumeName                                       | Yes                   | PV name, which must be the same as the PV name in :ref:`1 <cce_10_0379__li162841212145314>`.                                                               |
         +--------------------------------------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------+

   b. Run the following command to create a PVC:

      .. code-block::

         kubectl apply -f pvc-obs.yaml

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
                 - name: pvc-obs-volume    # Volume name, which must be the same as the volume name in the volumes field
                   mountPath: /data  # Location where the storage volume is mounted
               imagePullSecrets:
                 - name: default-secret
               volumes:
                 - name: pvc-obs-volume    # Volume name, which can be customized
                   persistentVolumeClaim:
                     claimName: pvc-obs    # Name of the created PVC

   b. Run the following command to create a workload to which the OBS volume is mounted:

      .. code-block::

         kubectl apply -f web-demo.yaml

      After the workload is created, you can try :ref:`Verifying Data Persistence and Sharing <cce_10_0379__section11593165910013>`.

.. _cce_10_0379__section11593165910013:

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

#. Run the following command to check the files in the **/data** path:

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

      The **static** file is retained, indicating that the data in the file system can be stored persistently.

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

.. _cce_10_0379__section16505832153318:

Related Operations
------------------

You can also perform the operations listed in :ref:`Table 4 <cce_10_0379__table1619535674020>`.

.. _cce_10_0379__table1619535674020:

.. table:: **Table 4** Related operations

   +--------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Operation                      | Description                                                                                                                                | Procedure                                                                                                                                                                                                                                         |
   +================================+============================================================================================================================================+===================================================================================================================================================================================================================================================+
   | Creating a storage volume (PV) | Create a PV on the CCE console.                                                                                                            | #. Choose **Storage** in the navigation pane. In the right pane, click the **PVs** tab. Click **Create PersistentVolume** in the upper right corner. In the dialog box displayed, configure parameters.                                           |
   |                                |                                                                                                                                            |                                                                                                                                                                                                                                                   |
   |                                |                                                                                                                                            |    -  **Volume Type**: Select **OBS**.                                                                                                                                                                                                            |
   |                                |                                                                                                                                            |                                                                                                                                                                                                                                                   |
   |                                |                                                                                                                                            |    -  **OBS**: Click **Select OBS**. On the displayed page, select the OBS volume that meets your requirements and click **OK**.                                                                                                                  |
   |                                |                                                                                                                                            |                                                                                                                                                                                                                                                   |
   |                                |                                                                                                                                            |    -  **PV Name**: Enter the PV name, which must be unique in a cluster.                                                                                                                                                                          |
   |                                |                                                                                                                                            |                                                                                                                                                                                                                                                   |
   |                                |                                                                                                                                            |    -  **Access Mode**: SFS volumes support only **ReadWriteMany**, indicating that a storage volume can be mounted to multiple nodes in read/write mode. For details, see :ref:`Volume Access Modes <cce_10_0378__section43881411172418>`.        |
   |                                |                                                                                                                                            |                                                                                                                                                                                                                                                   |
   |                                |                                                                                                                                            |    -  **Reclaim Policy**: **Delete** or **Retain** is supported. For details, see :ref:`PV Reclaim Policy <cce_10_0378__section19999142414413>`.                                                                                                  |
   |                                |                                                                                                                                            |                                                                                                                                                                                                                                                   |
   |                                |                                                                                                                                            |       .. note::                                                                                                                                                                                                                                   |
   |                                |                                                                                                                                            |                                                                                                                                                                                                                                                   |
   |                                |                                                                                                                                            |          If multiple PVs use the same underlying storage volume, use **Retain** to prevent the underlying volume from being deleted with a PV.                                                                                                    |
   |                                |                                                                                                                                            |                                                                                                                                                                                                                                                   |
   |                                |                                                                                                                                            |    -  **Access Key (AK/SK)**: (Recommended) Customize a secret if you want to assign different user permissions to different OBS storage devices. For details, see :ref:`Using a Custom Access Key (AK/SK) to Mount an OBS Volume <cce_10_0336>`. |
   |                                |                                                                                                                                            |                                                                                                                                                                                                                                                   |
   |                                |                                                                                                                                            |       Only secrets with the **secret.kubernetes.io/used-by = csi** label can be selected. The secret type is cfe/secure-opaque. If no secret is available, click **Create Secret** to create one.                                                 |
   |                                |                                                                                                                                            |                                                                                                                                                                                                                                                   |
   |                                |                                                                                                                                            |    -  **Mount Options**: Enter the mounting parameter key-value pairs. For details, see :ref:`Configuring OBS Mount Options <cce_10_0631>`.                                                                                                       |
   |                                |                                                                                                                                            |                                                                                                                                                                                                                                                   |
   |                                |                                                                                                                                            | #. Click **Create**.                                                                                                                                                                                                                              |
   +--------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Updating an access key         | Update the access key of object storage on the CCE console.                                                                                | #. Choose **Storage** in the navigation pane. In the right pane, click the **PVCs** tab. Click **More** in the **Operation** column of the target PVC and select **Update Access Key**.                                                           |
   |                                |                                                                                                                                            | #. Upload a key file in .csv format. For details, see :ref:`Obtaining an Access Key <cce_10_0336__section4633162355911>`. Click **OK**.                                                                                                           |
   |                                |                                                                                                                                            |                                                                                                                                                                                                                                                   |
   |                                |                                                                                                                                            |    .. note::                                                                                                                                                                                                                                      |
   |                                |                                                                                                                                            |                                                                                                                                                                                                                                                   |
   |                                |                                                                                                                                            |       After a global access key is updated, all pods mounted with the object storage that uses this access key can be accessed only after being restarted.                                                                                        |
   +--------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Viewing events                 | View event names, event types, number of occurrences, Kubernetes events, first occurrence time, and last occurrence time of the PVC or PV. | #. Choose **Storage** in the navigation pane. In the right pane, click the **PVCs** or **PVs** tab.                                                                                                                                               |
   |                                |                                                                                                                                            | #. Click **View Events** in the **Operation** column of the target PVC or PV to view events generated within one hour (events are retained for one hour).                                                                                         |
   +--------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Viewing a YAML file            | View, copy, or download the YAML file of a PVC or PV.                                                                                      | #. Choose **Storage** in the navigation pane. In the right pane, click the **PVCs** or **PVs** tab.                                                                                                                                               |
   |                                |                                                                                                                                            | #. Click **View YAML** in the **Operation** column of the target PVC or PV to view or download the YAML.                                                                                                                                          |
   +--------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
