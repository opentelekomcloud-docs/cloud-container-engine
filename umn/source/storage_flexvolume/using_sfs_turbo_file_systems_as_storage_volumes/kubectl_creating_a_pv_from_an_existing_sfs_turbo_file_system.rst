:original_name: cce_10_0332.html

.. _cce_10_0332:

(kubectl) Creating a PV from an Existing SFS Turbo File System
==============================================================

Scenario
--------

CCE allows you to use an existing SFS Turbo file system to create a PersistentVolume (PV). After the creation is successful, you can create a PersistentVolumeClaim (PVC) and bind it to the PV.

Notes and Constraints
---------------------

The following configuration example applies to clusters of Kubernetes 1.13 or earlier.

Procedure
---------

#. Log in to the SFS console, create a file system, and record the file system ID, shared path, and capacity.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create two YAML files for creating the PV and PVC. Assume that the file names are **pv-efs-example.yaml** and **pvc-efs-example.yaml**.

   **touch pv-efs-example.yaml** **pvc-efs-example.yaml**

   -  **Example YAML file for the PV:**

      .. code-block::

         apiVersion: v1
         kind: PersistentVolume
         metadata:
           name: pv-efs-example
           annotations:
             pv.kubernetes.io/provisioned-by: flexvolume-huawei.com/fuxiefs
         spec:
           accessModes:
           - ReadWriteMany
           capacity:
             storage: 100Gi
           claimRef:
             apiVersion: v1
             kind: PersistentVolumeClaim
             name: pvc-efs-example
             namespace: default
           flexVolume:
             driver: huawei.com/fuxiefs
             fsType: efs
             options:
               deviceMountPath: <your_deviceMountPath>  # Shared storage path of your SFS Turbo file.
               fsType: efs
               volumeID: 8962a2a2-a583-4b7f-bb74-fe76712d8414
           persistentVolumeReclaimPolicy: Delete
           storageClassName: efs-standard

      .. table:: **Table 1** Key parameters

         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                           |
         +===================================+=======================================================================================================================================================================================================+
         | driver                            | Storage driver used to mount the volume. Set it to **huawei.com/fuxiefs**.                                                                                                                            |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | deviceMountPath                   | Shared path of the SFS Turbo volume.                                                                                                                                                                  |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | volumeID                          | SFS Turbo volume ID.                                                                                                                                                                                  |
         |                                   |                                                                                                                                                                                                       |
         |                                   | To obtain the ID, log in to the CCE console, choose **Resource Management** > **Storage**, click the PVC name in the **SFS Turbo** tab page, and copy the PVC ID on the PVC details page.             |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storage                           | File system size.                                                                                                                                                                                     |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storageClassName                  | Volume type supported by SFS Turbo. The value can be **efs-standard** and **efs-performance**. Currently, SFS Turbo does not support dynamic creation; therefore, this parameter is not used for now. |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | spec.claimRef.apiVersion          | The value is fixed at **v1**.                                                                                                                                                                         |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | spec.claimRef.kind                | The value is fixed at **PersistentVolumeClaim**.                                                                                                                                                      |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | spec.claimRef.name                | The value is the same as the name of the PVC created in the next step.                                                                                                                                |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | spec.claimRef.namespace           | The value is the same as the namespace of the PVC created in the next step.                                                                                                                           |
         +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   -  **Example YAML file for the PVC:**

      .. code-block::

         apiVersion: v1
         kind: PersistentVolumeClaim
         metadata:
           annotations:
             volume.beta.kubernetes.io/storage-class: efs-standard
             volume.beta.kubernetes.io/storage-provisioner: flexvolume-huawei.com/fuxiefs
           name: pvc-efs-example
           namespace: default
         spec:
           accessModes:
           - ReadWriteMany
           resources:
             requests:
               storage: 100Gi
           volumeName: pv-efs-example

      .. table:: **Table 2** Key parameters

         +-----------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                                     | Description                                                                                                                                              |
         +===============================================+==========================================================================================================================================================+
         | volume.beta.kubernetes.io/storage-class       | Read/write mode supported by SFS Turbo. The value can be **efs-standard** or **efs-performance**. The value must be the same as that of the existing PV. |
         +-----------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------+
         | volume.beta.kubernetes.io/storage-provisioner | The field must be set to **flexvolume-huawei.com/fuxiefs**.                                                                                              |
         +-----------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storage                                       | Storage capacity, in the unit of Gi. The value must be the same as the storage size of the existing PV.                                                  |
         +-----------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------+
         | volumeName                                    | Name of the PV.                                                                                                                                          |
         +-----------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. note::

      The VPC to which the SFS Turbo file system belongs must be the same as the VPC of the ECS VM planned for the workload. Ports 111, 445, 2049, 2051, and 20048 must be enabled in the security groups.

#. Create the PV.

   **kubectl create -f pv-efs-example.yaml**

#. Create the PVC.

   **kubectl create -f pvc-efs-example.yaml**
