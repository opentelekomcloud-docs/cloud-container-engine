:original_name: cce_10_0620.html

.. _cce_10_0620:

Using an SFS File System Through a Dynamic PV
=============================================

This section describes how to use storage classes to dynamically create PVs and PVCs for data persistence and sharing in workloads.

Prerequisites
-------------

-  You have created a cluster and installed the :ref:`CCE Container Storage (Everest) <cce_10_0066>` add-on in the cluster.
-  To create a cluster using commands, ensure kubectl is used. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

Automatically Creating an SFS File System on the Console
--------------------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Dynamically create a PVC and PV.

   a. Choose **Storage** in the navigation pane. In the right pane, click the **PVCs** tab. Click **Create PVC** in the upper right corner. In the dialog box displayed, configure PVC parameters.

      +---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                             | Description                                                                                                                                                                                                                                                         |
      +=======================================+=====================================================================================================================================================================================================================================================================+
      | PVC Type                              | In this example, select **SFS**.                                                                                                                                                                                                                                    |
      +---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | PVC Name                              | Enter the PVC name, which must be unique in a namespace.                                                                                                                                                                                                            |
      +---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Creation Method                       | -  If no underlying storage is available, select **Dynamically provision** to create a PVC, PV, and underlying storage on the console in cascading mode.                                                                                                            |
      |                                       | -  If underlying storage is available, create a PV or use an existing PV to statically create a PVC. For details, see :ref:`Using an Existing SFS File System Through a Static PV <cce_10_0619>`.                                                                   |
      |                                       |                                                                                                                                                                                                                                                                     |
      |                                       | In this example, select **Dynamically provision**.                                                                                                                                                                                                                  |
      +---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Storage Classes                       | The default StorageClass of SFS volumes is **csi-nas**.                                                                                                                                                                                                             |
      |                                       |                                                                                                                                                                                                                                                                     |
      |                                       | You can customize a StorageClass and configure its reclaim policy and binding mode. For details, see :ref:`Creating a StorageClass Using the CCE Console <cce_10_0380__section11263115719212>`.                                                                     |
      +---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | (Optional) Storage Volume Name Prefix | Available only when the cluster version is v1.23.14-r0, v1.25.9-r0, v1.27.6-r0, v1.28.4-r0, or later, and Everest of v2.4.15 or later is installed in the cluster.                                                                                                  |
      |                                       |                                                                                                                                                                                                                                                                     |
      |                                       | This parameter specifies the name of the underlying storage that is automatically created. The actual underlying storage name is in the format of "Storage volume name prefix + PVC UID". If this parameter is left blank, the default prefix **pvc** will be used. |
      |                                       |                                                                                                                                                                                                                                                                     |
      |                                       | For example, if the storage volume name prefix is set to **test**, the actual underlying storage name is **test-**\ *{UID}*.                                                                                                                                        |
      +---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Access Mode                           | SFS volumes support only **ReadWriteMany**, indicating that a storage volume can be mounted to multiple nodes in read/write mode. For details, see :ref:`Volume Access Modes <cce_10_0378__section43881411172418>`.                                                 |
      +---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   b. Click **Create** to create a PVC and a PV.

      You can choose **Storage** in the navigation pane and view the created PVC and PV on the **PVCs** and **PVs** tab pages, respectively.

#. Create an application.

   a. Choose **Workloads** in the navigation pane. In the right pane, click the **Deployments** tab.

   b. Click **Create Workload** in the upper right corner. On the displayed page, click **Data Storage** in the **Container Information** area under **Container Settings** and choose **Add Volume** > **PVC**.

      Mount and use storage volumes, as shown in :ref:`Table 1 <cce_10_0620__cce_10_0619_table2529244345>`. For details about other parameters, see :ref:`Workloads <cce_10_0046>`.

      .. _cce_10_0620__cce_10_0619_table2529244345:

      .. table:: **Table 1** Mounting a storage volume

         +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                        |
         +===================================+====================================================================================================================================================================================================================================================================================================================================================================================================================================================+
         | PVC                               | Select an existing SFS volume.                                                                                                                                                                                                                                                                                                                                                                                                                     |
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

      In this example, the disk is mounted to the **/data** path of the container. The container data generated in this path is stored in the SFS file system.

   c. After the configuration, click **Create Workload**.

      After the workload is created, the data in the container mount directory will be persistently stored. Verify the storage by referring to :ref:`Verifying Data Persistence and Sharing <cce_10_0619__section11593165910013>`.

Automatically Creating an SFS File System Through kubectl
---------------------------------------------------------

#. Use kubectl to access the cluster.
#. Use **StorageClass** to dynamically create a PVC and PV.

   a. Create the **pvc-sfs-auto.yaml** file.

      .. code-block::

         apiVersion: v1
         kind: PersistentVolumeClaim
         metadata:
           name: pvc-sfs-auto
           namespace: default
           annotations:



             everest.io/csi.volume-name-prefix: test  # (Optional) Storage volume name prefix of the automatically created underlying storage
         spec:
           accessModes:
             - ReadWriteMany             # The value must be ReadWriteMany for SFS.
           resources:
             requests:
               storage: 1Gi             # SFS volume capacity
           storageClassName: csi-nas    # The StorageClass is SFS.

      .. table:: **Table 2** Key parameters

         +-----------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Mandatory             | Description                                                                                                                                                                                                                                                         |
         +===================================+=======================+=====================================================================================================================================================================================================================================================================+
         | storage                           | Yes                   | Requested capacity in the PVC, in Gi.                                                                                                                                                                                                                               |
         |                                   |                       |                                                                                                                                                                                                                                                                     |
         |                                   |                       | For SFS, this parameter is used only for verification and cannot be empty or **0**. Its value is fixed at **1**, and any value set will not take effect for SFS file systems.                                                                                       |
         +-----------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | everest.io/crypt-key-id           | No                    | If the StorageClass is **csi-nas**, you can determine whether to encrypt the underlying storage.                                                                                                                                                                    |
         |                                   |                       |                                                                                                                                                                                                                                                                     |
         |                                   |                       | This parameter is mandatory when an SFS system is encrypted. Enter the encryption key ID selected during SFS system creation. You can use a custom key or the default key named **sfs/default**.                                                                    |
         |                                   |                       |                                                                                                                                                                                                                                                                     |
         |                                   |                       | To obtain a key ID, log in to the DEW console, locate the key to be encrypted, and copy the key ID.                                                                                                                                                                 |
         +-----------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | everest.io/crypt-alias            | No                    | Key name, which is mandatory when you create an encrypted volume.                                                                                                                                                                                                   |
         |                                   |                       |                                                                                                                                                                                                                                                                     |
         |                                   |                       | To obtain a key name, log in to the DEW console, locate the key to be encrypted, and copy the key name.                                                                                                                                                             |
         +-----------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | everest.io/crypt-domain-id        | No                    | ID of the tenant to which the encrypted volume belongs. This parameter is mandatory for creating an encrypted volume.                                                                                                                                               |
         |                                   |                       |                                                                                                                                                                                                                                                                     |
         |                                   |                       | To obtain a tenant ID, hover the cursor over the username in the upper right corner of the ECS console, choose **My Credentials**, and copy the account ID.                                                                                                         |
         +-----------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | everest.io/csi.volume-name-prefix | No                    | (Optional) This parameter is available only when the cluster version is v1.23.14-r0, v1.25.9-r0, v1.27.6-r0, v1.28.4-r0, or later, and Everest of v2.4.15 or later is installed in the cluster.                                                                     |
         |                                   |                       |                                                                                                                                                                                                                                                                     |
         |                                   |                       | This parameter specifies the name of the underlying storage that is automatically created. The actual underlying storage name is in the format of "Storage volume name prefix + PVC UID". If this parameter is left blank, the default prefix **pvc** will be used. |
         |                                   |                       |                                                                                                                                                                                                                                                                     |
         |                                   |                       | Enter 1 to 26 characters that cannot start or end with a hyphen (-). Only lowercase letters, digits, and hyphens (-) are allowed.                                                                                                                                   |
         |                                   |                       |                                                                                                                                                                                                                                                                     |
         |                                   |                       | For example, if the storage volume name prefix is set to **test**, the actual underlying storage name is **test-**\ *{UID}*.                                                                                                                                        |
         +-----------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   b. Run the following command to create a PVC:

      .. code-block::

         kubectl apply -f pvc-sfs-auto.yaml

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
                 - name: pvc-sfs-volume    # Volume name, which must be the same as the volume name in the volumes field
                   mountPath: /data  # Location where the storage volume is mounted
               imagePullSecrets:
                 - name: default-secret
               volumes:
                 - name: pvc-sfs-volume    # Volume name, which can be customized
                   persistentVolumeClaim:
                     claimName: pvc-sfs-auto    # Name of the created PVC

   b. Run the following command to create a workload to which the SFS volume is mounted:

      .. code-block::

         kubectl apply -f web-demo.yaml

      After the workload is created, the data in the container mount directory will be persistently stored. Verify the storage by referring to :ref:`Verifying Data Persistence and Sharing <cce_10_0620__section11593165910013>`.

.. _cce_10_0620__section11593165910013:

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

Related Operations
------------------

You can also perform the operations listed in :ref:`Table 3 <cce_10_0620__table1619535674020>`.

.. _cce_10_0620__table1619535674020:

.. table:: **Table 3** Related operations

   +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Operation             | Description                                                                                                                                | Procedure                                                                                                                                                 |
   +=======================+============================================================================================================================================+===========================================================================================================================================================+
   | Viewing events        | View event names, event types, number of occurrences, Kubernetes events, first occurrence time, and last occurrence time of the PVC or PV. | #. Choose **Storage** in the navigation pane. In the right pane, click the **PVCs** or **PVs** tab.                                                       |
   |                       |                                                                                                                                            | #. Click **View Events** in the **Operation** column of the target PVC or PV to view events generated within one hour (events are retained for one hour). |
   +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Viewing a YAML file   | View, copy, or download the YAML file of a PVC or PV.                                                                                      | #. Choose **Storage** in the navigation pane. In the right pane, click the **PVCs** or **PVs** tab.                                                       |
   |                       |                                                                                                                                            | #. Click **View YAML** in the **Operation** column of the target PVC or PV to view or download the YAML.                                                  |
   +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------+
