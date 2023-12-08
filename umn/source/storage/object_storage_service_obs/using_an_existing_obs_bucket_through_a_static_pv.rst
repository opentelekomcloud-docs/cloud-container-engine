:original_name: cce_10_0379.html

.. _cce_10_0379:

Using an Existing OBS Bucket Through a Static PV
================================================

This section describes how to use an existing Object Storage Service (OBS) bucket to statically create PVs and PVCs and implement data persistence and sharing in workloads.

Prerequisites
-------------

-  You have created a cluster and installed the CSI add-on (:ref:`everest <cce_10_0066>`) in the cluster.
-  If you want to create a cluster using commands, use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.
-  You have created an OBS bucket. An OBS bucket of the parallel file system type can be selected only when it is in the same region as the cluster.

Constraints
-----------

-  Kata containers do not support OBS volumes.

-  When parallel file systems and object buckets are used, the group and permission of the mount point cannot be modified.

-  CCE allows you to use OBS parallel file systems by calling the OBS SDK or mounting a PVC through the **obsfs** tool provided by OBS. Each time an OBS parallel file system is mounted, an obsfs resident process is generated, as shown in the following figure.


   .. figure:: /_static/images/en-us_image_0000001647417468.png
      :alt: **Figure 1** obsfs resident process

      **Figure 1** obsfs resident process

   Reserve 1 GiB of memory for each obsfs process. For example, for a node with 4 vCPUs and 8 GiB of memory, the obsfs parallel file system should be mounted to **no more than** eight pods.

   .. note::

      An obsfs resident process runs on a node. If the consumed memory exceeds the upper limit of the node, the node malfunctions. On a node with 4 vCPUs and 8 GiB of memory, if more than 100 pods are mounted to parallel file systems, the node will be unavailable. Control the number of pods mounted to parallel file systems on a single node.

-  Multiple PVs can use the same OBS storage volume with the following restrictions:

   -  If multiple PVCs/PVs use the same underlying object storage volume, when you attempt to mount the volume to the same pod, the operation will fail because the **volumeHandle** values of these PVs are the same.
   -  The **persistentVolumeReclaimPolicy** parameter in the PVs must be set to **Retain**. Otherwise, when a PV is deleted, the associated underlying volume may be deleted. In this case, other PVs associated with the underlying volume malfunction.
   -  When the underlying volume is repeatedly used, enable isolation and protection for ReadWriteMany at the application layer to prevent data overwriting and loss.

Using an Existing OBS Bucket on the Console
-------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Statically create a PVC and PV.

   a. Choose **Storage** from the navigation pane, and click the **PersistentVolumeClaims (PVCs)** tab. Click **Create PVC** in the upper right corner. In the dialog box displayed, configure the PVC parameters.

      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                         |
      +===================================+=====================================================================================================================================================================================================================+
      | PVC Type                          | In this section, select **OBS**.                                                                                                                                                                                    |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PVC Name                          | Enter the PVC name, which must be unique in the same namespace.                                                                                                                                                     |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Creation Method                   | -  If underlying storage is available, create a storage volume or use an existing storage volume to statically create a PVC based on whether a PV has been created.                                                 |
      |                                   | -  If no underlying storage is available, select **Dynamically provision**. For details, see :ref:`Using an OBS Bucket Through a Dynamic PV <cce_10_0630>`.                                                         |
      |                                   |                                                                                                                                                                                                                     |
      |                                   | In this example, select **Create new** to create a PV and PVC at the same time on the console.                                                                                                                      |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PV\ :sup:`a`                      | Select an existing PV volume in the cluster. Create a PV in advance. For details, see "Creating a storage volume" in :ref:`Related Operations <cce_10_0379__section16505832153318>`.                                |
      |                                   |                                                                                                                                                                                                                     |
      |                                   | You do not need to specify this parameter in this example.                                                                                                                                                          |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | OBS\ :sup:`b`                     | Click **Select OBS**. On the displayed page, select the OBS bucket that meets your requirements and click **OK**.                                                                                                   |
      |                                   |                                                                                                                                                                                                                     |
      |                                   | .. note::                                                                                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                     |
      |                                   |    Currently, only parallel file systems are supported.                                                                                                                                                             |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PV Name\ :sup:`b`                 | Enter the PV name, which must be unique in the same cluster.                                                                                                                                                        |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Access Mode\ :sup:`b`             | OBS volumes support only **ReadWriteMany**, indicating that a storage volume can be mounted to multiple nodes in read/write mode. For details, see :ref:`Volume Access Modes <cce_10_0378__section43881411172418>`. |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Reclaim Policy\ :sup:`b`          | You can select **Delete** or **Retain** to specify the reclaim policy of the underlying storage when the PVC is deleted. For details, see :ref:`PV Reclaim Policy <cce_10_0378__section19999142414413>`.            |
      |                                   |                                                                                                                                                                                                                     |
      |                                   | .. note::                                                                                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                     |
      |                                   |    If multiple PVs use the same OBS volume, use **Retain** to avoid cascading deletion of underlying volumes.                                                                                                       |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Secret\ :sup:`b`                  | **Custom**: Customize a secret if you want to assign different user permissions to different OBS storage devices. For details, see :ref:`Using a Custom Access Key (AK/SK) to Mount an OBS Volume <cce_10_0336>`.   |
      |                                   |                                                                                                                                                                                                                     |
      |                                   | Only secrets with the **secret.kubernetes.io/used-by = csi** label can be selected. The secret type is cfe/secure-opaque. If no secret is available, click **Create Secret** to create one.                         |
      |                                   |                                                                                                                                                                                                                     |
      |                                   | -  **Name**: Enter a secret name.                                                                                                                                                                                   |
      |                                   | -  **Namespace**: Select the namespace where the secret is.                                                                                                                                                         |
      |                                   | -  **Access Key (AK/SK)**: Upload a key file in .csv format. For details, see :ref:`Obtaining an Access Key <cce_10_0336__section4633162355911>`.                                                                   |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Mount Options\ :sup:`b`           | Enter the mounting parameter key-value pairs. For details, see :ref:`Configuring OBS Mount Options <cce_10_0631>`.                                                                                                  |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

      .. note::

         a: The parameter is available when **Creation Method** is set to **Use existing**.

         b: The parameter is available when **Creation Method** is set to **Create new**.

   b. Click **Create** to create a PVC and a PV.

      You can choose **Storage** in the navigation pane and view the created PVC and PV on the **PersistentVolumeClaims (PVCs)** and **PersistentVolumes (PVs)** tab pages.

#. Create an application.

   a. In the navigation pane on the left, click **Workloads**. In the right pane, click the **Deployments** tab.

   b. Click **Create Workload** in the upper right corner. On the displayed page, click **Data Storage** in the **Container Settings** area and click **Add Volume** to select **PVC**.

      Mount and use storage volumes, as shown in :ref:`Table 1 <cce_10_0379__table2529244345>`. For details about other parameters, see :ref:`Workloads <cce_10_0046>`.

      .. _cce_10_0379__table2529244345:

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
         |                                   |    If a volume is mounted to a high-risk directory, use an account with minimum permissions to start the container. Otherwise, high-risk files on the host may be damaged.                                                                                                                                                                                                                                                                                  |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Subpath                           | Enter a subpath, for example, **tmp**, indicating that data in the mount path of the container will be stored in the **tmp** folder of the volume.                                                                                                                                                                                                                                                                                                          |
         |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
         |                                   | A subpath is used to mount a local volume so that the same data volume is used in a single pod. If this parameter is left blank, the root path is used by default.                                                                                                                                                                                                                                                                                          |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Permission                        | -  **Read-only**: You can only read the data in the mounted volumes.                                                                                                                                                                                                                                                                                                                                                                                        |
         |                                   | -  **Read/Write**: You can modify the data volumes mounted to the path. Newly written data is not migrated if the container is migrated, which may cause data loss.                                                                                                                                                                                                                                                                                         |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

      In this example, the disk is mounted to the **/data** path of the container. The container data generated in this path is stored in the OBS volume.

   c. After the configuration, click **Create Workload**.

      After the workload is created, the data in the container mount directory will be persistently stored. Verify the storage by referring to :ref:`PV Reclaim Policy <cce_10_0378__section19999142414413>`.

(kubectl) Using an Existing OBS Bucket
--------------------------------------

#. Use kubectl to connect to the cluster.
#. Create a PV.

   a. .. _cce_10_0379__li162841212145314:

      Create the **pv-obs.yaml** file.

      .. code-block::

         apiVersion: v1
         kind: PersistentVolume
         metadata:
           annotations:
             pv.kubernetes.io/provisioned-by: everest-csi-provisioner
             everest.io/reclaim-policy: retain-volume-only      # (Optional) The PV is deleted while the underlying volume is retained.
           name: pv-obs       # PV name.
         spec:
           accessModes:
           - ReadWriteMany    # Access mode. The value must be ReadWriteMany for OBS.
           capacity:
             storage: 1Gi     # OBS volume capacity.
           csi:
             driver: obs.csi.everest.io        # Dependent storage driver for the mounting.
             driver: obs.csi.everest.io        # Instance type.
             volumeHandle: <your_volume_id>    # Name of the OBS volume.
             volumeAttributes:
               storage.kubernetes.io/csiProvisionerIdentity: everest-csi-provisioner
               everest.io/obs-volume-type: STANDARD
               everest.io/region: <your_region>                        # Region where the OBS volume is.
             nodePublishSecretRef:            # Custom secret of the OBS volume.
               name: <your_secret_name>       # Custom secret name.
               namespace: <your_namespace>    # Namespace of the custom secret.
           persistentVolumeReclaimPolicy: Retain    # Reclaim policy.
           storageClassName: csi-obs               # Storage class name.
           mountOptions: []                         # Mount options.

      .. table:: **Table 2** Key parameters

         +-----------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                                     | Mandatory             | Description                                                                                                                                                                                                                                                                                         |
         +===============================================+=======================+=====================================================================================================================================================================================================================================================================================================+
         | everest.io/reclaim-policy: retain-volume-only | No                    | Optional.                                                                                                                                                                                                                                                                                           |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       | Currently, only **retain-volume-only** is supported.                                                                                                                                                                                                                                                |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       | This field is valid only when the everest version is 1.2.9 or later and the reclaim policy is **Delete**. If the reclaim policy is **Delete** and the current value is **retain-volume-only**, the associated PV is deleted while the underlying storage volume is retained, when a PVC is deleted. |
         +-----------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | fsType                                        | Yes                   | Instance type. The value can be **obsfs** or **s3fs**.                                                                                                                                                                                                                                              |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       | -  **obsfs**: Parallel file system, which is mounted using obsfs (recommended).                                                                                                                                                                                                                     |
         |                                               |                       | -  **s3fs**: Object bucket, which is mounted using s3fs.                                                                                                                                                                                                                                            |
         +-----------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | volumeHandle                                  | Yes                   | OBS volume name.                                                                                                                                                                                                                                                                                    |
         +-----------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | everest.io/obs-volume-type                    | Yes                   | OBS storage class.                                                                                                                                                                                                                                                                                  |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       | -  If **fsType** is set to **s3fs**, **STANDARD** (standard bucket) and **WARM** (infrequent access bucket) are supported.                                                                                                                                                                          |
         |                                               |                       | -  This parameter is invalid when **fsType** is set to **obsfs**.                                                                                                                                                                                                                                   |
         +-----------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | everest.io/region                             | Yes                   | Region where the OBS bucket is deployed.                                                                                                                                                                                                                                                            |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       | For details about the value of **region**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.                                                                                                                                                                |
         +-----------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | nodePublishSecretRef                          | No                    | Access key (AK/SK) used for mounting the object storage volume. You can use the AK/SK to create a secret and mount it to the PV. For details, see :ref:`Using a Custom Access Key (AK/SK) to Mount an OBS Volume <cce_10_0336>`.                                                                    |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       | An example is as follows:                                                                                                                                                                                                                                                                           |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       | .. code-block::                                                                                                                                                                                                                                                                                     |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       |    nodePublishSecretRef:                                                                                                                                                                                                                                                                            |
         |                                               |                       |      name: secret-demo                                                                                                                                                                                                                                                                              |
         |                                               |                       |      namespace: default                                                                                                                                                                                                                                                                             |
         +-----------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | mountOptions                                  | No                    | Mount options. For details, see :ref:`Configuring OBS Mount Options <cce_10_0631>`.                                                                                                                                                                                                                 |
         +-----------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | persistentVolumeReclaimPolicy                 | Yes                   | A reclaim policy is supported when the cluster version is or later than 1.19.10 and the everest version is or later than 1.2.9.                                                                                                                                                                     |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       | The **Delete** and **Retain** reclaim policies are supported. For details, see :ref:`PV Reclaim Policy <cce_10_0378__section19999142414413>`. If multiple PVs use the same OBS volume, use **Retain** to avoid cascading deletion of underlying volumes.                                            |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       | **Delete**:                                                                                                                                                                                                                                                                                         |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       | -  If **everest.io/reclaim-policy** is not specified, both the PV and storage resources are deleted when a PVC is deleted.                                                                                                                                                                          |
         |                                               |                       | -  If **everest.io/reclaim-policy** is set to **retain-volume-only**, when a PVC is deleted, the PV is deleted but the storage resources are retained.                                                                                                                                              |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       | **Retain**: When a PVC is deleted, the PV and underlying storage resources are not deleted. Instead, you must manually delete these resources. After that, the PV is in the **Released** status and cannot be bound to the PVC again.                                                               |
         +-----------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storage                                       | Yes                   | Storage capacity, in Gi.                                                                                                                                                                                                                                                                            |
         |                                               |                       |                                                                                                                                                                                                                                                                                                     |
         |                                               |                       | For OBS buckets, this field is used only for verification (cannot be empty or 0). Its value is fixed at **1**, and any value you set does not take effect for OBS buckets.                                                                                                                          |
         +-----------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storageClassName                              | Yes                   | The storage class name of OBS volumes is **csi-obs**.                                                                                                                                                                                                                                               |
         +-----------------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

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
           storageClassName: csi-obs       # Storage class name, which must be the same as that of the PV.
           volumeName: pv-obs    # PV name.

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
         | storageClassName                                 | Yes                   | Storage class name, which must be the same as the storage class of the PV in :ref:`1 <cce_10_0379__li162841212145314>`.                                    |
         |                                                  |                       |                                                                                                                                                            |
         |                                                  |                       | The storage class name of OBS volumes is **csi-obs**.                                                                                                      |
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
                 - name: pvc-obs-volume    #Volume name, which must be the same as the volume name in the volumes field.
                   mountPath: /data  #Location where the storage volume is mounted.
               imagePullSecrets:
                 - name: default-secret
               volumes:
                 - name: pvc-obs-volume    #Volume name, which can be customized.
                   persistentVolumeClaim:
                     claimName: pvc-obs    #Name of the created PVC.

   b. Run the following command to create an application to which the OBS volume is mounted:

      .. code-block::

         kubectl apply -f web-demo.yaml

      After the workload is created, you can try :ref:`Verifying Data Persistence and Sharing <cce_10_0379__section11593165910013>`.

.. _cce_10_0379__section11593165910013:

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

.. _cce_10_0379__section16505832153318:

Related Operations
------------------

You can also perform the operations listed in :ref:`Table 4 <cce_10_0379__table1619535674020>`.

.. _cce_10_0379__table1619535674020:

.. table:: **Table 4** Related operations

   +--------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Operation                      | Description                                                                                                                                        | Procedure                                                                                                                                                                                                                                  |
   +================================+====================================================================================================================================================+============================================================================================================================================================================================================================================+
   | Creating a storage volume (PV) | Create a PV on the CCE console.                                                                                                                    | #. Choose **Storage** from the navigation pane, and click the **PersistentVolumes (PVs)** tab. Click **Create Volume** in the upper right corner. In the dialog box displayed, configure the parameters.                                   |
   |                                |                                                                                                                                                    |                                                                                                                                                                                                                                            |
   |                                |                                                                                                                                                    |    -  **Volume Type**: Select **OBS**.                                                                                                                                                                                                     |
   |                                |                                                                                                                                                    |                                                                                                                                                                                                                                            |
   |                                |                                                                                                                                                    |    -  **OBS**: Click **Select OBS**. On the displayed page, select the OBS storage that meets your requirements and click **OK**.                                                                                                          |
   |                                |                                                                                                                                                    |                                                                                                                                                                                                                                            |
   |                                |                                                                                                                                                    |    -  **PV Name**: Enter the PV name, which must be unique in the same cluster.                                                                                                                                                            |
   |                                |                                                                                                                                                    |                                                                                                                                                                                                                                            |
   |                                |                                                                                                                                                    |    -  **Access Mode**: SFS volumes support only **ReadWriteMany**, indicating that a storage volume can be mounted to multiple nodes in read/write mode. For details, see :ref:`Volume Access Modes <cce_10_0378__section43881411172418>`. |
   |                                |                                                                                                                                                    |                                                                                                                                                                                                                                            |
   |                                |                                                                                                                                                    |    -  **Reclaim Policy**: **Delete** or **Retain**. For details, see :ref:`PV Reclaim Policy <cce_10_0378__section19999142414413>`.                                                                                                        |
   |                                |                                                                                                                                                    |                                                                                                                                                                                                                                            |
   |                                |                                                                                                                                                    |       .. note::                                                                                                                                                                                                                            |
   |                                |                                                                                                                                                    |                                                                                                                                                                                                                                            |
   |                                |                                                                                                                                                    |          If multiple PVs use the same underlying storage volume, use **Retain** to prevent the underlying volume from being deleted with a PV.                                                                                             |
   |                                |                                                                                                                                                    |                                                                                                                                                                                                                                            |
   |                                |                                                                                                                                                    |    -  **Custom**: Customize a secret if you want to assign different user permissions to different OBS storage devices. For details, see :ref:`Using a Custom Access Key (AK/SK) to Mount an OBS Volume <cce_10_0336>`.                    |
   |                                |                                                                                                                                                    |                                                                                                                                                                                                                                            |
   |                                |                                                                                                                                                    |       Only secrets with the **secret.kubernetes.io/used-by = csi** label can be selected. The secret type is cfe/secure-opaque. If no secret is available, click **Create Secret** to create one.                                          |
   |                                |                                                                                                                                                    |                                                                                                                                                                                                                                            |
   |                                |                                                                                                                                                    |    -  **Mount Options**: Enter the mounting parameter key-value pairs. For details, see :ref:`Configuring OBS Mount Options <cce_10_0631>`.                                                                                                |
   |                                |                                                                                                                                                    |                                                                                                                                                                                                                                            |
   |                                |                                                                                                                                                    | #. Click **Create**.                                                                                                                                                                                                                       |
   +--------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Updating an access key         | Update the access key of object storage on the CCE console.                                                                                        | #. Choose **Storage** from the navigation pane, and click the **PersistentVolumeClaims (PVCs)** tab. Click **More** > **Update Access Key** in the **Operation** column of the PVC.                                                        |
   |                                |                                                                                                                                                    | #. Upload a key file in .csv format. For details, see :ref:`Obtaining an Access Key <cce_10_0336__section4633162355911>`. Click **OK**.                                                                                                    |
   |                                |                                                                                                                                                    |                                                                                                                                                                                                                                            |
   |                                |                                                                                                                                                    |    .. note::                                                                                                                                                                                                                               |
   |                                |                                                                                                                                                    |                                                                                                                                                                                                                                            |
   |                                |                                                                                                                                                    |       After a global access key is updated, all pods mounted with the object storage that uses this access key can be accessed only after being restarted.                                                                                 |
   +--------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Viewing events                 | You can view event names, event types, number of occurrences, Kubernetes events, first occurrence time, and last occurrence time of the PVC or PV. | #. Choose **Storage** from the navigation pane, and click the **PersistentVolumeClaims (PVCs)** or **PersistentVolumes (PVs)** tab.                                                                                                        |
   |                                |                                                                                                                                                    | #. Click **View Events** in the **Operation** column of the target PVC or PV to view events generated within one hour (event data is retained for one hour).                                                                               |
   +--------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Viewing a YAML file            | You can view, copy, and download the YAML files of a PVC or PV.                                                                                    | #. Choose **Storage** from the navigation pane, and click the **PersistentVolumeClaims (PVCs)** or **PersistentVolumes (PVs)** tab.                                                                                                        |
   |                                |                                                                                                                                                    | #. Click **View YAML** in the **Operation** column of the target PVC or PV to view or download the YAML.                                                                                                                                   |
   +--------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
