:original_name: cce_10_0638.html

.. _cce_10_0638:

Using a Temporary Path
======================

A temporary path is of the Kubernetes-native emptyDir type. Its lifecycle is the same as that of a pod. Memory can be specified as the storage medium. When the pod is deleted, the emptyDir volume is deleted and its data is lost.

Using the Console to Use a Temporary Path
-----------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane on the left, click **Workloads**. In the right pane, click the **Deployments** tab.

#. Click **Create Workload** in the upper right corner of the page. In the **Container Settings** area, click the **Data Storage** tab and click **Add Volume** > **EmptyDir**.

#. Mount and use storage volumes, as shown in :ref:`Table 1 <cce_10_0638__table1867417102475>`. For details about other parameters, see :ref:`Workloads <cce_10_0046>`.

   .. _cce_10_0638__table1867417102475:

   .. table:: **Table 1** Mounting an EV

      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
      +===================================+=============================================================================================================================================================================================================================================================================================================================================================================================================================================================+
      | Storage Medium                    | **Memory**:                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
      |                                   | -  You can select this option to improve the running speed, but the storage capacity is subject to the memory size. This mode is applicable when data volume is small and efficient read and write is required.                                                                                                                                                                                                                                             |
      |                                   | -  If this function is disabled, data is stored in hard disks, which applies to a large amount of data with low requirements on reading and writing efficiency.                                                                                                                                                                                                                                                                                             |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
      |                                   | .. note::                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
      |                                   |    -  If **Memory** is selected, pay attention to the memory size. If the storage capacity exceeds the memory size, an OOM event occurs.                                                                                                                                                                                                                                                                                                                    |
      |                                   |    -  If **Memory** is selected, the size of an EV is the same as pod specifications.                                                                                                                                                                                                                                                                                                                                                                       |
      |                                   |    -  If **Memory** is not selected, EVs will not occupy the system memory.                                                                                                                                                                                                                                                                                                                                                                                 |
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

Using kubectl to Use a Temporary Path
-------------------------------------

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
                  - name: vol-emptydir     # Volume name, which must be the same as the volume name in the volumes field.
                    mountPath: /tmp        # Path to which an EV is mounted.
            imagePullSecrets:
              - name: default-secret
            volumes:
              - name: vol-emptydir         # Volume name, which can be customized.
                emptyDir:
                  medium: Memory          # EV disk medium: If this parameter is set to Memory, the memory is enabled. If this parameter is left blank, the native default storage medium is used.
                  sizeLimit: 1Gi          # Volume capacity.

#. Create a workload.

   **kubectl apply -f nginx-emptydir.yaml**
