:original_name: cce_10_0726.html

.. _cce_10_0726:

Using a Local EV
================

Local Ephemeral Volumes (EVs) are stored in EV :ref:`storage pools <cce_10_0642>`. Local EVs deliver better performance than the default storage medium of native emptyDir and support scale-out.

Prerequisites
-------------

-  You have created a cluster and installed the CSI add-on (:ref:`Everest <cce_10_0066>`) in the cluster.
-  If you want to create a cluster using commands, use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.
-  To use a local EV, import a data disk of a node to the local EV storage pool. For details, see :ref:`Importing an EV to a Storage Pool <cce_10_0725>`.

Constraints
-----------

-  Local EVs are supported only when the cluster version is v1.21.2-r0 or later and the Everest add-on version is 1.2.29 or later.
-  Do not manually delete the corresponding storage pool or detach data disks from the node. Otherwise, exceptions such as data loss may occur.
-  Ensure that the **/var/lib/kubelet/pods/** directory is not mounted to the pod on the node. Otherwise, the pod, mounted with such volumes, may fail to be deleted.

Using the Console to Mount a Local EV
-------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane on the left, click **Workloads**. In the right pane, click the **Deployments** tab.

#. Click **Create Workload** in the upper right corner of the page. In the **Container Settings** area, click the **Data Storage** tab and click **Add Volume** > **Local Ephemeral Volume (emptyDir)**.

#. Mount and use storage volumes, as shown in :ref:`Table 1 <cce_10_0726__table2529244345>`. For details about other parameters, see :ref:`Workloads <cce_10_0046>`.

   .. _cce_10_0726__table2529244345:

   .. table:: **Table 1** Mounting a local EV

      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
      +===================================+=============================================================================================================================================================================================================================================================================================================================================================================================================================================================+
      | Capacity                          | Capacity of the requested storage volume.                                                                                                                                                                                                                                                                                                                                                                                                                   |
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
      | Subpath                           | Enter the subpath of the storage volume and mount a path in the storage volume to the container. In this way, different folders of the same storage volume can be used in a single pod. tmp, for example, indicates that data in the mount path of the container is stored in the **tmp** folder of the storage volume. If this parameter is left blank, the root path is used by default.                                                                  |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Permission                        | -  **Read-only**: You can only read the data in the mounted volumes.                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                   | -  **Read/Write**: You can modify the data volumes mounted to the path. Newly written data will not be migrated if the container is migrated, which may cause data loss.                                                                                                                                                                                                                                                                                    |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. After the configuration, click **Create Workload**.

Using kubectl to Mount a Local EV
---------------------------------

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create a file named **nginx-emptydir.yaml** and edit it.

   **vi nginx-emptydir.yaml**

   Content of the YAML file:

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: nginx-emptydir
        namespace: default
      spec:
        replicas: 2
        selector:
          matchLabels:
            app: nginx-emptydir
        template:
          metadata:
            labels:
              app: nginx-emptydir
          spec:
            containers:
              - name: container-1
                image: nginx:latest
                volumeMounts:
                  - name: vol-emptydir         # Volume name, which must be the same as the volume name in the volumes field.
                    mountPath: /tmp            # Path to which an EV is mounted.
            imagePullSecrets:
              - name: default-secret
            volumes:
              - name: vol-emptydir             # Volume name, which can be customized.
                emptyDir:
                  medium: LocalVolume          # If the disk medium of emptyDir is set to LocalVolume, the local EV is used.
                  sizeLimit: 1Gi               # Volume capacity.

#. Create a workload.

   **kubectl apply -f nginx-emptydir.yaml**
