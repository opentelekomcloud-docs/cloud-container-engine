:original_name: cce_10_0635.html

.. _cce_10_0635:

Dynamically Mounting a Local PV to a StatefulSet
================================================

Application Scenarios
---------------------

Dynamic mounting is available only for creating a :ref:`StatefulSet <cce_10_0048>`. It is implemented through a volume claim template (`volumeClaimTemplates <https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/#volume-claim-templates>`__ field) and depends on dynamic creation of PVs through StorageClass. In this mode, each pod in a multi-pod StatefulSet is associated with a unique PVC and PV. After a pod is rescheduled, the original data can still be mounted to it based on the PVC name. In the common mounting mode for a Deployment, if ReadWriteMany is supported, multiple pods of the Deployment will be mounted to the same underlying storage.

Prerequisites
-------------

-  You have created a cluster and installed the CSI add-on (:ref:`Everest <cce_10_0066>`) in the cluster.
-  To create a cluster using commands, ensure kubectl is used. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.
-  You have imported a data disk of a node to the local PV storage pool. For details, see :ref:`Importing a PV to a Storage Pool <cce_10_0642>`.

Dynamically Mounting a Local PV on the Console
----------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. Choose **Workloads** in the navigation pane. In the right pane, click the **StatefulSets** tab.

#. Click **Create Workload** in the upper right corner. On the displayed page, click **Data Storage** in the **Container Settings** area and click **Add Volume** to select **VolumeClaimTemplate**.

#. Click **Create PVC**. In the dialog box displayed, configure the volume claim template parameters.

   Click **Create**.

   +---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                             | Description                                                                                                                                                                                                                                             |
   +=======================================+=========================================================================================================================================================================================================================================================+
   | PVC Type                              | In this section, select **Local PV**.                                                                                                                                                                                                                   |
   +---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | PVC Name                              | Enter the name of the PVC. After a PVC is created, a suffix is automatically added based on the number of pods. The format is <*Custom PVC name*>-<*Serial number*>, for example, example-0.                                                            |
   +---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Creation Method                       | You can only select **Dynamically provision** to create a PVC, PV, and underlying storage on the console in cascading mode.                                                                                                                             |
   +---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Storage Classes                       | The storage class of local PVs is **csi-local-topology**.                                                                                                                                                                                               |
   +---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | (Optional) Storage Volume Name Prefix | Available only when the cluster version is v1.23.14-r0, v1.25.9-r0, v1.27.6-r0, v1.28.4-r0, or later, and Everest of v2.4.15 or later is installed in the cluster.                                                                                      |
   |                                       |                                                                                                                                                                                                                                                         |
   |                                       | This parameter specifies the name of the underlying storage that is automatically created. The actual underlying storage name is in the format of "PV name prefix + PVC UID". If this parameter is left blank, the default prefix **pvc** will be used. |
   |                                       |                                                                                                                                                                                                                                                         |
   |                                       | For example, if the PV name prefix is set to **test**, the actual underlying storage name is **test-**\ *{UID}*.                                                                                                                                        |
   +---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Capacity (GiB)                        | Capacity of the requested storage volume.                                                                                                                                                                                                               |
   +---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Access Mode                           | Local PVs support only **ReadWriteOnce**, indicating that a PV can be mounted to one node in read/write mode. For details, see :ref:`Volume Access Modes <cce_10_0378__section43881411172418>`.                                                         |
   +---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Storage Pool                          | View the imported storage pool. For details about how to import a new data volume to the storage pool, see :ref:`Importing a PV to a Storage Pool <cce_10_0642>`.                                                                                       |
   +---------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Enter the path to which the volume is mounted.

   .. table:: **Table 1** Mounting a storage volume

      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
      +===================================+==============================================================================================================================================================================================================================================================================================================================================================================================================================================================+
      | Mount Path                        | Enter a mount path, for example, **/tmp**.                                                                                                                                                                                                                                                                                                                                                                                                                   |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
      |                                   | This parameter specifies a container path to which a data volume will be mounted. Do not mount the volume to a system directory such as **/** or **/var/run**. Otherwise, containers will be malfunctional. Mount the volume to an empty directory. If the directory is not empty, ensure that there are no files that affect container startup. Otherwise, the files will be replaced, leading to container startup failures or workload creation failures. |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
      |                                   | .. important::                                                                                                                                                                                                                                                                                                                                                                                                                                               |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
      |                                   |    NOTICE:                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
      |                                   |    If a volume is mounted to a high-risk directory, use an account with minimum permissions to start the container. Otherwise, high-risk files on the host may be damaged.                                                                                                                                                                                                                                                                                   |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Subpath                           | Enter the subpath of the storage volume and mount a path in the storage volume to the container. In this way, different folders of the same storage volume can be used in a single pod. **tmp**, for example, indicates that data in the mount path of the container is stored in the **tmp** folder of the storage volume. If this parameter is left blank, the root path is used by default.                                                               |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Permission                        | -  **Read-only**: You can only read the data in the mounted volumes.                                                                                                                                                                                                                                                                                                                                                                                         |
      |                                   | -  **Read-write**: You can modify the data volumes mounted to the path. Newly written data will not be migrated if the container is migrated, which may cause data loss.                                                                                                                                                                                                                                                                                     |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   In this example, the disk is mounted to the **/data** path of the container. The container data generated in this path is stored in the local PV.

#. Dynamically mount and use storage volumes. For details about other parameters, see :ref:`Creating a StatefulSet <cce_10_0048>`. After the configuration, click **Create Workload**.

   After the workload is created, the data in the container mount directory will be persistently stored. Verify the storage by referring to :ref:`Verifying Data Persistence <cce_10_0635__section11593165910013>`.

Dynamically Mounting a Local PV Through kubectl
-----------------------------------------------

#. Use kubectl to access the cluster.

#. Create a file named **statefulset-local.yaml**. In this example, the local PV is mounted to the **/data** path.

   .. code-block::

      apiVersion: apps/v1
      kind: StatefulSet
      metadata:
        name: statefulset-local
        namespace: default
      spec:
        selector:
          matchLabels:
            app: statefulset-local
        template:
          metadata:
            labels:
              app: statefulset-local
          spec:
            containers:
              - name: container-1
                image: nginx:latest
                volumeMounts:
                  - name: pvc-local          # The value must be the same as that in the volumeClaimTemplates field.
                    mountPath: /data         # Location where the storage volume is mounted
            imagePullSecrets:
              - name: default-secret
        serviceName: statefulset-local       # Headless Service name
        replicas: 2
        volumeClaimTemplates:
          - apiVersion: v1
            kind: PersistentVolumeClaim
            metadata:
              name: pvc-local
              namespace: default
              annotations:
                everest.io/csi.volume-name-prefix: test  # (Optional) PV name prefix of the automatically created underlying storage
            spec:
              accessModes:
                - ReadWriteOnce               # The value must be ReadWriteOnce for local PVs.
              resources:
                requests:
                  storage: 10Gi               # Storage volume capacity
              storageClassName: csi-local-topology      # StorageClass is local PV.
      ---
      apiVersion: v1
      kind: Service
      metadata:
        name: statefulset-local   # Headless Service name
        namespace: default
        labels:
          app: statefulset-local
      spec:
        selector:
          app: statefulset-local
        clusterIP: None
        ports:
          - name: statefulset-local
            targetPort: 80
            nodePort: 0
            port: 80
            protocol: TCP
        type: ClusterIP

   .. table:: **Table 2** Key parameters

      +-----------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Mandatory             | Description                                                                                                                                                                                                                                             |
      +===================================+=======================+=========================================================================================================================================================================================================================================================+
      | everest.io/csi.volume-name-prefix | No                    | (Optional) This parameter is available only when the cluster version is v1.23.14-r0, v1.25.9-r0, v1.27.6-r0, v1.28.4-r0, or later, and Everest of v2.4.15 or later is installed in the cluster.                                                         |
      |                                   |                       |                                                                                                                                                                                                                                                         |
      |                                   |                       | This parameter specifies the name of the underlying storage that is automatically created. The actual underlying storage name is in the format of "PV name prefix + PVC UID". If this parameter is left blank, the default prefix **pvc** will be used. |
      |                                   |                       |                                                                                                                                                                                                                                                         |
      |                                   |                       | Enter 1 to 26 characters that cannot start or end with a hyphen (-). Only lowercase letters, digits, and hyphens (-) are allowed.                                                                                                                       |
      |                                   |                       |                                                                                                                                                                                                                                                         |
      |                                   |                       | For example, if the PV name prefix is set to **test**, the actual underlying storage name is **test-**\ *{UID}*.                                                                                                                                        |
      +-----------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | storage                           | Yes                   | Requested capacity in the PVC, in Gi.                                                                                                                                                                                                                   |
      +-----------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | storageClassName                  | Yes                   | The storage class of local PVs is **csi-local-topology**.                                                                                                                                                                                               |
      +-----------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Run the following command to create a workload to which the local PV is mounted:

   .. code-block::

      kubectl apply -f statefulset-local.yaml

   After the workload is created, you can try :ref:`Verifying Data Persistence <cce_10_0635__section11593165910013>`.

.. _cce_10_0635__section11593165910013:

Verifying Data Persistence
--------------------------

#. View the deployed application and files.

   a. Run the following command to view the created pod:

      .. code-block::

         kubectl get pod | grep statefulset-local

      Expected output:

      .. code-block::

         statefulset-local-0          1/1     Running   0             45s
         statefulset-local-1          1/1     Running   0             28s

   b. Run the following command to check whether the local PV has been mounted to the **/data** path:

      .. code-block::

         kubectl exec statefulset-local-0 -- df | grep data

      Expected output:

      .. code-block::

         /dev/mapper/vg--everest--localvolume--persistent-pvc-local              10255636     36888  10202364   0% /data

   c. Run the following command to check the files in the **/data** path:

      .. code-block::

         kubectl exec statefulset-local-0 -- ls /data

      Expected output:

      .. code-block::

         lost+found

#. Run the following command to create a file named **static** in the **/data** path:

   .. code-block::

      kubectl exec statefulset-local-0 --  touch /data/static

#. Run the following command to check the files in the **/data** path:

   .. code-block::

      kubectl exec statefulset-local-0 -- ls /data

   Expected output:

   .. code-block::

      lost+found
      static

#. Run the following command to delete the pod named **web-local-auto-0**:

   .. code-block::

      kubectl delete pod statefulset-local-0

   Expected output:

   .. code-block::

      pod "statefulset-local-0" deleted

#. After the deletion, the StatefulSet controller automatically creates a replica with the same name. Run the following command to check whether the files in the **/data** path have been modified:

   .. code-block::

      kubectl exec statefulset-local-0 -- ls /data

   Expected output:

   .. code-block::

      lost+found
      static

   The **static** file is retained, indicating that the data in the local PV can be stored persistently.

Related Operations
------------------

You can also perform the operations listed in :ref:`Table 3 <cce_10_0635__cce_10_0634_table1619535674020>`.

.. _cce_10_0635__cce_10_0634_table1619535674020:

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
