:original_name: cce_10_0377.html

.. _cce_10_0377:

hostPath
========

hostPath is used for mounting the file directory of the host where the container is located to the specified mount point of the container. If the container needs to access **/etc/hosts**, use hostPath to map **/etc/hosts**.

.. important::

   -  Avoid using hostPath volumes as much as possible, as they are prone to security risks. If hostPath volumes must be used, they can only be applied to files or directories and mounted in read-only mode.
   -  After the pod to which a hostPath volume is mounted is deleted, the data in the hostPath volume is retained.

Mounting a hostPath Volume on the Console
-----------------------------------------

You can mount a path on the host to a specified container path. A hostPath volume is usually used to **store workload logs permanently** or used by workloads that need to **access internal data structure of the Docker engine on the host**.

#. Log in to the CCE console.

#. When creating a workload, click **Data Storage** in **Container Settings**. Click **Add Volume** and choose **hostPath** from the drop-down list.

#. Set parameters for adding a local volume, as listed in :ref:`Table 1 <cce_10_0377__table14312815449>`.

   .. _cce_10_0377__table14312815449:

   .. table:: **Table 1** Setting parameters for mounting a hostPath volume

      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
      +===================================+==============================================================================================================================================================================================================================================================================================================================================================================================================================================================+
      | Volume Type                       | Select **HostPath**.                                                                                                                                                                                                                                                                                                                                                                                                                                         |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | HostPath                          | Path of the host to which the local volume is to be mounted, for example, **/etc/hosts**.                                                                                                                                                                                                                                                                                                                                                                    |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
      |                                   | .. note::                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
      |                                   |    **HostPath** cannot be set to the root directory **/**. Otherwise, the mounting fails. Mount paths can be as follows:                                                                                                                                                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
      |                                   |    -  **/opt/xxxx** (excluding **/opt/cloud**)                                                                                                                                                                                                                                                                                                                                                                                                               |
      |                                   |    -  **/mnt/xxxx** (excluding **/mnt/paas**)                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   |    -  **/tmp/xxx**                                                                                                                                                                                                                                                                                                                                                                                                                                           |
      |                                   |    -  **/var/xxx** (excluding key directories such as **/var/lib**, **/var/script**, and **/var/paas**)                                                                                                                                                                                                                                                                                                                                                      |
      |                                   |    -  **/xxxx** (It cannot conflict with the system directory, such as **bin**, **lib**, **home**, **root**, **boot**, **dev**, **etc**, **lost+found**, **mnt**, **proc**, **sbin**, **srv**, **tmp**, **var**, **media**, **opt**, **selinux**, **sys**, and **usr**.)                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
      |                                   |    Do not set this parameter to **/home/paas**, **/var/paas**, **/var/lib**, **/var/script**, **/mnt/paas**, or **/opt/cloud**. Otherwise, the system or node installation will fail.                                                                                                                                                                                                                                                                        |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
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

#. After the configuration, click **Create Workload**.

Mounting a hostPath Volume Through kubectl
------------------------------------------

#. Use kubectl to access the cluster.

#. Create a file named **nginx-hostpath.yaml** and edit it.

   **vi nginx-hostpath.yaml**

   The content of the YAML file is as follows. Mount the **/data** directory on the node to the **/data** directory in the container.

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: nginx-hostpath
        namespace: default
      spec:
        replicas: 2
        selector:
          matchLabels:
            app: nginx-hostpath
        template:
          metadata:
            labels:
              app: nginx-hostpath
          spec:
            containers:
              - name: container-1
                image: nginx:latest
                volumeMounts:
                  - name: vol-hostpath         # Volume name, which must be the same as the volume name in the volumes field.
                    mountPath: /data           # Location where the storage volume is mounted
            imagePullSecrets:
              - name: default-secret
            volumes:
              - name: vol-hostpath             # Volume name, which can be customized.
                hostPath:
                  path: /data                  # Directory location on the host node.

#. Create a workload.

   **kubectl apply -f nginx-hostpath.yaml**
