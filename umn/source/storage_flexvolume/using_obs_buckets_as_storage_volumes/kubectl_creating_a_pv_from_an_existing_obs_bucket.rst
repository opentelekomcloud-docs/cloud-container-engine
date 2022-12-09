:original_name: cce_01_0326.html

.. _cce_01_0326:

(kubectl) Creating a PV from an Existing OBS Bucket
===================================================

Scenario
--------

CCE allows you to use an existing OBS bucket to create a PersistentVolume (PV). You can create a PersistentVolumeClaim (PVC) and bind it to the PV.

Prerequisites
-------------

-  You have created a CCE cluster and installed the FlexVolume plug-in (:ref:`storage-driver <cce_01_0127>`) in the cluster.
-  The AK/SK has been uploaded. For details, see :ref:`Preparations <cce_01_0324__section14271608324>`.

Notes and Constraints
---------------------

The following configuration example applies to clusters of Kubernetes 1.13 or earlier.

Procedure
---------

#. Log in to the OBS console, create an OBS bucket, and record the bucket name and storage class.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_01_0107>`.

#. Create two YAML files for creating the PV and PVC. Assume that the file names are **pv-obs-example.yaml** and **pvc-obs-example.yaml**.

   **touch pv-obs-example.yaml** **pvc-obs-example.yaml**

   +-----------------------------+------------------------------+-----------------------------------------------------+
   | Kubernetes Version          | Description                  | YAML Example                                        |
   +=============================+==============================+=====================================================+
   | 1.11 <= K8s version <= 1.13 | Clusters from v1.11 to v1.13 | :ref:`Example YAML <cce_01_0326__li45671840132016>` |
   +-----------------------------+------------------------------+-----------------------------------------------------+
   | K8s version = 1.9           | Clusters of v1.9             | :ref:`Example YAML <cce_01_0326__li154036581589>`   |
   +-----------------------------+------------------------------+-----------------------------------------------------+

   **Clusters from v1.11 to v1.13**

   -  .. _cce_01_0326__li45671840132016:

      **Example YAML file for the PV:**

      .. code-block::

         apiVersion: v1
         kind: PersistentVolume
         metadata:
           name: pv-obs-example
           annotations:
             pv.kubernetes.io/provisioned-by: flexvolume-huawei.com/fuxiobs
         spec:
           accessModes:
           - ReadWriteMany
           capacity:
             storage: 1Gi
           claimRef:
             apiVersion: v1
             kind: PersistentVolumeClaim
             name: pvc-obs-example
             namespace: default
           flexVolume:
             driver: huawei.com/fuxiobs
             fsType: obs
             options:
               fsType: obs
               region: eu-de
               storage_class: STANDARD
               volumeID: test-obs
           persistentVolumeReclaimPolicy: Delete
           storageClassName: obs-standard

      .. table:: **Table 1** Key parameters

         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                   |
         +===================================+===============================================================================================================================================================================================+
         | driver                            | Storage driver used to mount the volume. Set the driver to **huawei.com/fuxiobs** for the OBS volume.                                                                                         |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storage_class                     | Storage class, including **STANDARD** (standard bucket) and **STANDARD_IA** (infrequent access bucket).                                                                                       |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | region                            | For details about the value of **region**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.                                                          |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | volumeID                          | OBS bucket name.                                                                                                                                                                              |
         |                                   |                                                                                                                                                                                               |
         |                                   | To obtain the name, log in to the CCE console, choose **Resource Management** > **Storage**, click the PVC name in the **OBS** tab page, and copy the PV name on the **PV Details** tab page. |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storage                           | Storage capacity in the unit of Gi. The value is fixed at **1Gi**.                                                                                                                            |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storageClassName                  | Storage class supported by OBS, including **obs-standard** (standard bucket) and **obs-standard-ia** (infrequent access bucket).                                                              |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | spec.claimRef.apiVersion          | The value is fixed at **v1**.                                                                                                                                                                 |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | spec.claimRef.kind                | The value is fixed at **PersistentVolumeClaim**.                                                                                                                                              |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | spec.claimRef.name                | The value is the same as the name of the PVC created in the next step.                                                                                                                        |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | spec.claimRef.namespace           | The value is the same as the namespace of the PVC created in the next step.                                                                                                                   |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   -  **Example YAML file for the PVC:**

      .. code-block::

         apiVersion: v1
         kind: PersistentVolumeClaim
         metadata:
           annotations:
             volume.beta.kubernetes.io/storage-class: obs-standard
             volume.beta.kubernetes.io/storage-provisioner: flexvolume-huawei.com/fuxiobs
           name: pvc-obs-example
           namespace: default
         spec:
           accessModes:
           - ReadWriteMany
           resources:
             requests:
               storage: 1Gi
           volumeName: pv-obs-example

      .. table:: **Table 2** Key parameters

         +-----------------------------------------------+-------------------------------------------------------------------------------------+
         | Parameter                                     | Description                                                                         |
         +===============================================+=====================================================================================+
         | volume.beta.kubernetes.io/storage-class       | Storage class supported by OBS, including **obs-standard** and **obs-standard-ia**. |
         +-----------------------------------------------+-------------------------------------------------------------------------------------+
         | volume.beta.kubernetes.io/storage-provisioner | Must be set to **flexvolume-huawei.com/fuxiobs**.                                   |
         +-----------------------------------------------+-------------------------------------------------------------------------------------+
         | volumeName                                    | Name of the PV.                                                                     |
         +-----------------------------------------------+-------------------------------------------------------------------------------------+
         | storage                                       | Storage capacity in the unit of Gi. The value is fixed at **1Gi**.                  |
         +-----------------------------------------------+-------------------------------------------------------------------------------------+

   **Clusters of v1.9**

   -  .. _cce_01_0326__li154036581589:

      **Example YAML file for the PV:**

      .. code-block::

         apiVersion: v1
         kind: PersistentVolume
         metadata:
           name: pv-obs-example
           namespace: default
         spec:
           accessModes:
           - ReadWriteMany
           capacity:
             storage: 1Gi
           flexVolume:
             driver: huawei.com/fuxiobs
             fsType: obs
             options:
               fsType: obs
               kubernetes.io/namespace: default
               region: eu-de
               storage_class: STANDARD
               volumeID: test-obs
           persistentVolumeReclaimPolicy: Delete
           storageClassName: obs-standard

      .. table:: **Table 3** Key parameters

         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                         | Description                                                                                                                                                                                   |
         +===================================+===============================================================================================================================================================================================+
         | driver                            | Storage driver used to mount the volume. Set the driver to **huawei.com/fuxiobs** for the OBS volume.                                                                                         |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storage_class                     | Storage class, including **STANDARD** (standard bucket) and **STANDARD_IA** (infrequent access bucket).                                                                                       |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | region                            | For details about the value of **region**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.                                                          |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | volumeID                          | OBS bucket name.                                                                                                                                                                              |
         |                                   |                                                                                                                                                                                               |
         |                                   | To obtain the name, log in to the CCE console, choose **Resource Management** > **Storage**, click the PVC name in the **OBS** tab page, and copy the PV name on the **PV Details** tab page. |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storage                           | Storage capacity in the unit of Gi. The value is fixed at **1Gi**.                                                                                                                            |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storageClassName                  | Storage class supported by OBS, including **obs-standard** (standard bucket) and **obs-standard-ia** (infrequent access bucket).                                                              |
         +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   -  **Example YAML file for the PVC:**

      .. code-block::

         apiVersion: v1
         kind: PersistentVolumeClaim
         metadata:
           annotations:
             volume.beta.kubernetes.io/storage-class: obs-standard
             volume.beta.kubernetes.io/storage-provisioner: flexvolume-huawei.com/fuxiobs
           name: pvc-obs-example
           namespace: default
         spec:
           accessModes:
           - ReadWriteMany
           resources:
             requests:
               storage: 1Gi
           volumeName: pv-obs-example
           volumeNamespace: default

      .. table:: **Table 4** Key parameters

         +-----------------------------------------------+-------------------------------------------------------------------------------------+
         | Parameter                                     | Description                                                                         |
         +===============================================+=====================================================================================+
         | volume.beta.kubernetes.io/storage-class       | Storage class supported by OBS, including **obs-standard** and **obs-standard-ia**. |
         +-----------------------------------------------+-------------------------------------------------------------------------------------+
         | volume.beta.kubernetes.io/storage-provisioner | Must be set to **flexvolume-huawei.com/fuxiobs**.                                   |
         +-----------------------------------------------+-------------------------------------------------------------------------------------+
         | volumeName                                    | Name of the PV.                                                                     |
         +-----------------------------------------------+-------------------------------------------------------------------------------------+
         | storage                                       | Storage capacity in the unit of Gi. The value is fixed at **1Gi**.                  |
         +-----------------------------------------------+-------------------------------------------------------------------------------------+

#. Create the PV.

   **kubectl create -f pv-obs-example.yaml**

#. Create the PVC.

   **kubectl create -f pvc-obs-example.yaml**
