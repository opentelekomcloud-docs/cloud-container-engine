:original_name: cce_10_0319.html

.. _cce_10_0319:

(kubectl) Creating a PV from an Existing SFS File System
========================================================

Notes and Constraints
---------------------

The following configuration example applies to clusters of Kubernetes 1.13 or earlier.

Procedure
---------

#. Log in to the SFS console, create a file system, and record the file system ID, shared path, and capacity.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create two YAML files for creating the PV and PVC. Assume that the file names are **pv-sfs-example.yaml** and **pvc-sfs-example.yaml**.

   **touch pv-sfs-example.yaml** **pvc-sfs-example.yaml**

   +----------------------------+------------------------------+-----------------------------------------------------+
   | Kubernetes Cluster Version | Description                  | YAML Example                                        |
   +============================+==============================+=====================================================+
   | 1.11 <= K8s version < 1.13 | Clusters from v1.11 to v1.13 | :ref:`Example YAML <cce_10_0319__li1252510101515>`  |
   +----------------------------+------------------------------+-----------------------------------------------------+
   | K8s version = 1.9          | Clusters of v1.9             | :ref:`Example YAML <cce_10_0319__li10858156164514>` |
   +----------------------------+------------------------------+-----------------------------------------------------+

   **Clusters from v1.11 to v1.13**

   -  .. _cce_10_0319__li1252510101515:

      **Example YAML file for the PV:**

      .. code-block::

         apiVersion: v1
         kind: PersistentVolume
         metadata:
           name: pv-sfs-example
           annotations:
             pv.kubernetes.io/provisioned-by: flexvolume-huawei.com/fuxinfs
         spec:
           accessModes:
           - ReadWriteMany
           capacity:
             storage: 10Gi
           claimRef:
             apiVersion: v1
             kind: PersistentVolumeClaim
             name: pvc-sfs-example
             namespace: default
           flexVolume:
             driver: huawei.com/fuxinfs
             fsType: nfs
             options:
               deviceMountPath: <your_deviceMountPath>  # Shared storage path of your file.
               fsType: nfs
               volumeID: f6976f9e-2493-419b-97ca-d7816008d91c
           persistentVolumeReclaimPolicy: Delete
           storageClassName: nfs-rw

      .. table:: **Table 1** Key parameters

         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                         |
         +===================================+=====================================================================================================================================================================================+
         | driver                            | Storage driver used to mount the volume. Set the driver to **huawei.com/fuxinfs** for the file system.                                                                              |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | deviceMountPath                   | Shared path of the file system.                                                                                                                                                     |
         |                                   |                                                                                                                                                                                     |
         |                                   | On the management console, choose **Service List** > **Storage** > **Scalable File Service**. You can obtain the shared path of the file system from the **Mount Address** column.  |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | volumeID                          | File system ID.                                                                                                                                                                     |
         |                                   |                                                                                                                                                                                     |
         |                                   | To obtain the ID, log in to the CCE console, choose **Resource Management** > **Storage**, click the PVC name in the **SFS** tab page, and copy the PVC ID on the PVC details page. |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storage                           | File system size.                                                                                                                                                                   |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storageClassName                  | Read/write mode supported by the file system. Currently, **nfs-rw** and **nfs-ro** are supported.                                                                                   |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | spec.claimRef.apiVersion          | The value is fixed at **v1**.                                                                                                                                                       |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | spec.claimRef.kind                | The value is fixed at **PersistentVolumeClaim**.                                                                                                                                    |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | spec.claimRef.name                | The value is the same as the name of the PVC created in the next step.                                                                                                              |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | spec.claimRef.namespace           | Namespace of the PVC. The value is the same as the namespace of the PVC created in the next step.                                                                                   |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   -  **Example YAML file for the PVC:**

      .. code-block::

         apiVersion: v1
         kind: PersistentVolumeClaim
         metadata:
           annotations:
             volume.beta.kubernetes.io/storage-class: nfs-rw
             volume.beta.kubernetes.io/storage-provisioner: flexvolume-huawei.com/fuxinfs
           name: pvc-sfs-example
           namespace: default
         spec:
           accessModes:
           - ReadWriteMany
           resources:
             requests:
               storage: 10Gi
           volumeName: pv-sfs-example

      .. table:: **Table 2** Key parameters

         +-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                                     | Description                                                                                                                                   |
         +===============================================+===============================================================================================================================================+
         | volume.beta.kubernetes.io/storage-class       | Read/write mode supported by the file system. **nfs-rw** and **nfs-ro** are supported. The value must be the same as that of the existing PV. |
         +-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
         | volume.beta.kubernetes.io/storage-provisioner | Must be set to **flexvolume-huawei.com/fuxinfs**.                                                                                             |
         +-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
         | storage                                       | Storage capacity, in the unit of Gi. The value must be the same as the storage size of the existing PV.                                       |
         +-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
         | volumeName                                    | Name of the PV.                                                                                                                               |
         +-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+

   **Clusters of v1.9**

   -  .. _cce_10_0319__li10858156164514:

      **Example YAML file for the PV:**

      .. code-block::

         apiVersion: v1
         kind: PersistentVolume
         metadata:
           name: pv-sfs-example
           namespace: default
         spec:
           accessModes:
           - ReadWriteMany
           capacity:
             storage: 10Gi
           flexVolume:
             driver: huawei.com/fuxinfs
             fsType: nfs
             options:
               deviceMountPath: <your_deviceMountPath>  # Shared storage path of your file.
               fsType: nfs
               kubernetes.io/namespace: default
               volumeID: f6976f9e-2493-419b-97ca-d7816008d91c
           persistentVolumeReclaimPolicy: Delete
           storageClassName: nfs-rw

      .. table:: **Table 3** Key parameters

         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                         |
         +===================================+=====================================================================================================================================================================================+
         | driver                            | Storage driver used to mount the volume. Set the driver to **huawei.com/fuxinfs** for the file system.                                                                              |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | deviceMountPath                   | Shared path of the file system.                                                                                                                                                     |
         |                                   |                                                                                                                                                                                     |
         |                                   | On the management console, choose **Service List** > **Storage** > **Scalable File Service**. You can obtain the shared path of the file system from the **Mount Address** column.  |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | volumeID                          | File system ID.                                                                                                                                                                     |
         |                                   |                                                                                                                                                                                     |
         |                                   | To obtain the ID, log in to the CCE console, choose **Resource Management** > **Storage**, click the PVC name in the **SFS** tab page, and copy the PVC ID on the PVC details page. |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storage                           | File system size.                                                                                                                                                                   |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storageClassName                  | Read/write mode supported by the file system. Currently, **nfs-rw** and **nfs-ro** are supported.                                                                                   |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   -  **Example YAML file for the PVC:**

      .. code-block::

         apiVersion: v1
         kind: PersistentVolumeClaim
         metadata:
           annotations:
             volume.beta.kubernetes.io/storage-class: nfs-rw
             volume.beta.kubernetes.io/storage-provisioner: flexvolume-huawei.com/fuxinfs
           name: pvc-sfs-example
           namespace: default
         spec:
           accessModes:
           - ReadWriteMany
           resources:
             requests:
               storage: 10Gi
           volumeName: pv-sfs-example
           volumeNamespace: default

      .. table:: **Table 4** Key parameters

         +-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                                     | Description                                                                                                                                   |
         +===============================================+===============================================================================================================================================+
         | volume.beta.kubernetes.io/storage-class       | Read/write mode supported by the file system. **nfs-rw** and **nfs-ro** are supported. The value must be the same as that of the existing PV. |
         +-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
         | volume.beta.kubernetes.io/storage-provisioner | The field must be set to **flexvolume-huawei.com/fuxinfs**.                                                                                   |
         +-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
         | storage                                       | Storage capacity, in the unit of Gi. The value must be the same as the storage size of the existing PV.                                       |
         +-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
         | volumeName                                    | Name of the PV.                                                                                                                               |
         +-----------------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+

   .. note::

      The VPC to which the file system belongs must be the same as the VPC of the ECS VM to which the workload is planned.

#. Create the PV.

   **kubectl create -f pv-sfs-example.yaml**

#. Create the PVC.

   **kubectl create -f pvc-sfs-example.yaml**
