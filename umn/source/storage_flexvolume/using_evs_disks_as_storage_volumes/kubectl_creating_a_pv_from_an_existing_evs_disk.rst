:original_name: cce_01_0313.html

.. _cce_01_0313:

(kubectl) Creating a PV from an Existing EVS Disk
=================================================

Scenario
--------

CCE allows you to create a PersistentVolume (PV) using an existing EVS disk. After the PV is created, you can create a PersistentVolumeClaim (PVC) and bind it to the PV.

Prerequisites
-------------

You have created a CCE cluster and installed the FlexVolume plug-in (:ref:`storage-driver <cce_01_0127>`) in the cluster.

Notes and Constraints
---------------------

The following configuration example applies to clusters of Kubernetes 1.13 or earlier.

Procedure
---------

#. Log in to the EVS console, create an EVS disk, and record the volume ID, capacity, and disk type of the EVS disk.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_01_0107>`.

#. Create two YAML files for creating the PersistentVolume (PV) and PersistentVolumeClaim (PVC). Assume that the file names are **pv-evs-example.yaml** and **pvc-evs-example.yaml**.

   **touch pv-evs-example.yaml** **pvc-evs-example.yaml**

   +-------------------------------+--------------------------------+-----------------------------------------------------+
   | Kubernetes Version            | Description                    | YAML Example                                        |
   +===============================+================================+=====================================================+
   | 1.11.7 <= K8s version <= 1.13 | Clusters from v1.11.7 to v1.13 | :ref:`Example YAML <cce_01_0313__li0648350102513>`  |
   +-------------------------------+--------------------------------+-----------------------------------------------------+
   | 1.11 <= K8s version < 1.11.7  | Clusters from v1.11 to v1.11.7 | :ref:`Example YAML <cce_01_0313__li19211184720504>` |
   +-------------------------------+--------------------------------+-----------------------------------------------------+
   | K8s version = 1.9             | Clusters of v1.9               | :ref:`Example YAML <cce_01_0313__li813222310297>`   |
   +-------------------------------+--------------------------------+-----------------------------------------------------+

   **Clusters from v1.11.7 to v1.13**

   -  .. _cce_01_0313__li0648350102513:

      **Example YAML file for the PV:**

      .. code-block::

         apiVersion: v1
         kind: PersistentVolume
         metadata:
           labels:
             failure-domain.beta.kubernetes.io/region: eu-de
             failure-domain.beta.kubernetes.io/zone:  eu-de-01
           annotations:
             pv.kubernetes.io/provisioned-by: flexvolume-huawei.com/fuxivol
           name: pv-evs-example
         spec:
           accessModes:
           - ReadWriteOnce
           capacity:
             storage: 10Gi
           claimRef:
             apiVersion: v1
             kind: PersistentVolumeClaim
             name: pvc-evs-example
             namespace: default
           flexVolume:
             driver: huawei.com/fuxivol
             fsType: ext4
             options:
               disk-mode: SCSI
               fsType: ext4
               volumeID: 0992dbda-6340-470e-a74e-4f0db288ed82
           persistentVolumeReclaimPolicy: Delete
           storageClassName: sas

      .. table:: **Table 1** Key parameters

         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                                | Description                                                                                                                                                                                                                                                                                                               |
         +==========================================+===========================================================================================================================================================================================================================================================================================================================+
         | failure-domain.beta.kubernetes.io/region | Region where the cluster is located.                                                                                                                                                                                                                                                                                      |
         |                                          |                                                                                                                                                                                                                                                                                                                           |
         |                                          | For details about the value of **region**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.                                                                                                                                                                                      |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | failure-domain.beta.kubernetes.io/zone   | AZ where the EVS volume is created. It must be the same as the AZ planned for the workload.                                                                                                                                                                                                                               |
         |                                          |                                                                                                                                                                                                                                                                                                                           |
         |                                          | For details about the value of **zone**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.                                                                                                                                                                                        |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storage                                  | EVS volume capacity in the unit of Gi.                                                                                                                                                                                                                                                                                    |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storageClassName                         | EVS disk type. Supported values: Common I/O (SATA), High I/O (SAS), and Ultra-high I/O (SSD)High I/O (SAS) and Ultra-high I/O (SSD)                                                                                                                                                                                       |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | driver                                   | Storage driver.                                                                                                                                                                                                                                                                                                           |
         |                                          |                                                                                                                                                                                                                                                                                                                           |
         |                                          | For EVS disks, set this parameter to **huawei.com/fuxivol**.                                                                                                                                                                                                                                                              |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | volumeID                                 | Volume ID of the EVS disk.                                                                                                                                                                                                                                                                                                |
         |                                          |                                                                                                                                                                                                                                                                                                                           |
         |                                          | To obtain the volume ID, log in to the CCE console, choose **Resource Management** > **Storage**, click the PVC name in the **EVS** tab page, and copy the PVC ID on the PVC details page.                                                                                                                                |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | disk-mode                                | Device type of the EVS disk. The value is **VBD** or **SCSI**.                                                                                                                                                                                                                                                            |
         |                                          |                                                                                                                                                                                                                                                                                                                           |
         |                                          | For CCE clusters earlier than v1.11.7, you do not need to set this field. The value defaults to **VBD**.                                                                                                                                                                                                                  |
         |                                          |                                                                                                                                                                                                                                                                                                                           |
         |                                          | This field is mandatory for CCE clusters from v1.11.7 to v1.13 that use Linux x86. As the EVS volumes dynamically provisioned by a PVC are created from SCSI EVS disks, you are advised to choose **SCSI** when manually creating volumes (static PVs). Volumes in the VBD mode can still be used after cluster upgrades. |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | spec.claimRef.apiVersion                 | The value is fixed at **v1**.                                                                                                                                                                                                                                                                                             |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | spec.claimRef.kind                       | The value is fixed at **PersistentVolumeClaim**.                                                                                                                                                                                                                                                                          |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | spec.claimRef.name                       | PVC name. The value is the same as the name of the PVC created in the next step.                                                                                                                                                                                                                                          |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | spec.claimRef.namespace                  | Namespace of the PVC. The value is the same as the namespace of the PVC created in the next step.                                                                                                                                                                                                                         |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   -  **Example YAML file for the PVC:**

      .. code-block::

         apiVersion: v1
         kind: PersistentVolumeClaim
         metadata:
           annotations:
             volume.beta.kubernetes.io/storage-class: sas
             volume.beta.kubernetes.io/storage-provisioner: flexvolume-huawei.com/fuxivol
           labels:
             failure-domain.beta.kubernetes.io/region: eu-de
             failure-domain.beta.kubernetes.io/zone: eu-de-01
           name: pvc-evs-example
           namespace: default
         spec:
           accessModes:
           - ReadWriteOnce
           resources:
             requests:
               storage: 10Gi
           volumeName: pv-evs-example

      .. table:: **Table 2** Key parameters

         +-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                                     | Description                                                                                                                          |
         +===============================================+======================================================================================================================================+
         | volume.beta.kubernetes.io/storage-class       | Storage class, which must be the same as that of the existing PV.                                                                    |
         +-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
         | volume.beta.kubernetes.io/storage-provisioner | The field must be set to **flexvolume-huawei.com/fuxivol**.                                                                          |
         +-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
         | failure-domain.beta.kubernetes.io/region      | Region where the cluster is located.                                                                                                 |
         |                                               |                                                                                                                                      |
         |                                               | For details about the value of **region**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__. |
         +-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
         | failure-domain.beta.kubernetes.io/zone        | AZ where the EVS volume is created. It must be the same as the AZ planned for the workload.                                          |
         |                                               |                                                                                                                                      |
         |                                               | For details about the value of **zone**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.   |
         +-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
         | storage                                       | Requested capacity in the PVC, in Gi.                                                                                                |
         |                                               |                                                                                                                                      |
         |                                               | The value must be the same as the storage size of the existing PV.                                                                   |
         +-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
         | volumeName                                    | Name of the PV.                                                                                                                      |
         +-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+

   **Clusters from v1.11 to v1.11.7**

   -  .. _cce_01_0313__li19211184720504:

      **Example YAML file for the PV:**

      .. code-block::

         apiVersion: v1
         kind: PersistentVolume
         metadata:
           labels:
             failure-domain.beta.kubernetes.io/region: eu-de
             failure-domain.beta.kubernetes.io/zone:
           name: pv-evs-example
         spec:
           accessModes:
           - ReadWriteOnce
           capacity:
             storage: 10Gi
           flexVolume:
             driver: huawei.com/fuxivol
             fsType: ext4
             options:
               fsType: ext4
               volumeID: 0992dbda-6340-470e-a74e-4f0db288ed82
           persistentVolumeReclaimPolicy: Delete
           storageClassName: sas

      .. table:: **Table 3** Key parameters

         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                                | Description                                                                                                                                                                                                                                                                                                               |
         +==========================================+===========================================================================================================================================================================================================================================================================================================================+
         | failure-domain.beta.kubernetes.io/region | Region where the cluster is located.                                                                                                                                                                                                                                                                                      |
         |                                          |                                                                                                                                                                                                                                                                                                                           |
         |                                          | For details about the value of **region**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.                                                                                                                                                                                      |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | failure-domain.beta.kubernetes.io/zone   | AZ where the EVS volume is created. It must be the same as the AZ planned for the workload.                                                                                                                                                                                                                               |
         |                                          |                                                                                                                                                                                                                                                                                                                           |
         |                                          | For details about the value of **zone**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.                                                                                                                                                                                        |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storage                                  | EVS volume capacity in the unit of Gi.                                                                                                                                                                                                                                                                                    |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storageClassName                         | EVS disk type. Supported values: Common I/O (SATA), High I/O (SAS), and Ultra-high I/O (SSD)High I/O (SAS) and Ultra-high I/O (SSD)                                                                                                                                                                                       |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | driver                                   | Storage driver.                                                                                                                                                                                                                                                                                                           |
         |                                          |                                                                                                                                                                                                                                                                                                                           |
         |                                          | For EVS disks, set this parameter to **huawei.com/fuxivol**.                                                                                                                                                                                                                                                              |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | volumeID                                 | Volume ID of the EVS disk.                                                                                                                                                                                                                                                                                                |
         |                                          |                                                                                                                                                                                                                                                                                                                           |
         |                                          | To obtain the volume ID, log in to the CCE console, choose **Resource Management** > **Storage**, click the PVC name in the **EVS** tab page, and copy the PVC ID on the PVC details page.                                                                                                                                |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | disk-mode                                | Device type of the EVS disk. The value is **VBD** or **SCSI**.                                                                                                                                                                                                                                                            |
         |                                          |                                                                                                                                                                                                                                                                                                                           |
         |                                          | For CCE clusters earlier than v1.11.7, you do not need to set this field. The default value is **VBD**.                                                                                                                                                                                                                   |
         |                                          |                                                                                                                                                                                                                                                                                                                           |
         |                                          | This field is mandatory for CCE clusters from v1.11.7 to v1.13 that use Linux x86. As the EVS volumes dynamically provisioned by a PVC are created from SCSI EVS disks, you are advised to choose **SCSI** when manually creating volumes (static PVs). Volumes in the VBD mode can still be used after cluster upgrades. |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   -  **Example YAML file for the PVC:**

      .. code-block::

         apiVersion: v1
         kind: PersistentVolumeClaim
         metadata:
           annotations:
             volume.beta.kubernetes.io/storage-class: sas
             volume.beta.kubernetes.io/storage-provisioner: flexvolume-huawei.com/fuxivol
           labels:
             failure-domain.beta.kubernetes.io/region: eu-de
             failure-domain.beta.kubernetes.io/zone: eu-de-01
           name: pvc-evs-example
           namespace: default
         spec:
           accessModes:
           - ReadWriteOnce
           resources:
             requests:
               storage: 10Gi
           volumeName: pv-evs-example

      .. table:: **Table 4** Key parameters

         +-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                                     | Description                                                                                                                          |
         +===============================================+======================================================================================================================================+
         | volume.beta.kubernetes.io/storage-class       | Storage class. The value can be **sas** or **ssd**. The value must be the same as that of the existing PV.                           |
         +-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
         | volume.beta.kubernetes.io/storage-provisioner | The field must be set to **flexvolume-huawei.com/fuxivol**.                                                                          |
         +-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
         | failure-domain.beta.kubernetes.io/region      | Region where the cluster is located.                                                                                                 |
         |                                               |                                                                                                                                      |
         |                                               | For details about the value of **region**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__. |
         +-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
         | failure-domain.beta.kubernetes.io/zone        | AZ where the EVS volume is created. It must be the same as the AZ planned for the workload.                                          |
         |                                               |                                                                                                                                      |
         |                                               | For details about the value of **zone**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.   |
         +-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
         | storage                                       | Requested capacity in the PVC, in Gi.                                                                                                |
         |                                               |                                                                                                                                      |
         |                                               | The value must be the same as the storage size of the existing PV.                                                                   |
         +-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
         | volumeName                                    | Name of the PV.                                                                                                                      |
         +-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+

   **Clusters of v1.9**

   -  .. _cce_01_0313__li813222310297:

      **Example YAML file for the PV:**

      .. code-block::

         apiVersion: v1
         kind: PersistentVolume
         metadata:
           labels:
             failure-domain.beta.kubernetes.io/region: eu-de
             failure-domain.beta.kubernetes.io/zone:
           name: pv-evs-example
           namespace: default
         spec:
           accessModes:
           - ReadWriteOnce
           capacity:
             storage: 10Gi
           flexVolume:
             driver: huawei.com/fuxivol
             fsType: ext4
             options:
               fsType: ext4
               kubernetes.io/namespace: default
               volumeID: 0992dbda-6340-470e-a74e-4f0db288ed82
           persistentVolumeReclaimPolicy: Delete
           storageClassName: sas

      .. table:: **Table 5** Key parameters

         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                                | Description                                                                                                                                                                                                                                                                                                               |
         +==========================================+===========================================================================================================================================================================================================================================================================================================================+
         | failure-domain.beta.kubernetes.io/region | Region where the cluster is located.                                                                                                                                                                                                                                                                                      |
         |                                          |                                                                                                                                                                                                                                                                                                                           |
         |                                          | For details about the value of **region**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.                                                                                                                                                                                      |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | failure-domain.beta.kubernetes.io/zone   | AZ where the EVS volume is created. It must be the same as the AZ planned for the workload.                                                                                                                                                                                                                               |
         |                                          |                                                                                                                                                                                                                                                                                                                           |
         |                                          | For details about the value of **zone**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.                                                                                                                                                                                        |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storage                                  | EVS volume capacity in the unit of Gi.                                                                                                                                                                                                                                                                                    |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | storageClassName                         | EVS disk type. Supported values: Common I/O (SATA), High I/O (SAS), and Ultra-high I/O (SSD)High I/O (SAS) and Ultra-high I/O (SSD)                                                                                                                                                                                       |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | driver                                   | Storage driver.                                                                                                                                                                                                                                                                                                           |
         |                                          |                                                                                                                                                                                                                                                                                                                           |
         |                                          | For EVS disks, set this parameter to **huawei.com/fuxivol**.                                                                                                                                                                                                                                                              |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | volumeID                                 | Volume ID of the EVS disk.                                                                                                                                                                                                                                                                                                |
         |                                          |                                                                                                                                                                                                                                                                                                                           |
         |                                          | To obtain the volume ID, log in to the CCE console, choose **Resource Management** > **Storage**, click the PVC name in the **EVS** tab page, and copy the PVC ID on the PVC details page.                                                                                                                                |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | disk-mode                                | Device type of the EVS disk. The value is **VBD** or **SCSI**.                                                                                                                                                                                                                                                            |
         |                                          |                                                                                                                                                                                                                                                                                                                           |
         |                                          | For CCE clusters earlier than v1.11.7, you do not need to set this field. The default value is **VBD**.                                                                                                                                                                                                                   |
         |                                          |                                                                                                                                                                                                                                                                                                                           |
         |                                          | This field is mandatory for CCE clusters from v1.11.7 to v1.13 that use Linux x86. As the EVS volumes dynamically provisioned by a PVC are created from SCSI EVS disks, you are advised to choose **SCSI** when manually creating volumes (static PVs). Volumes in the VBD mode can still be used after cluster upgrades. |
         +------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   -  **Example YAML file for the PVC:**

      .. code-block::

         apiVersion: v1
         kind: PersistentVolumeClaim
         metadata:
           annotations:
             volume.beta.kubernetes.io/storage-class: sas
             volume.beta.kubernetes.io/storage-provisioner: flexvolume-huawei.com/fuxivol
           labels:
             failure-domain.beta.kubernetes.io/region: eu-de
             failure-domain.beta.kubernetes.io/zone:
           name: pvc-evs-example
           namespace: default
         spec:
           accessModes:
           - ReadWriteOnce
           resources:
             requests:
               storage: 10Gi
           volumeName: pv-evs-example
           volumeNamespace: default

      .. table:: **Table 6** Key parameters

         +-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter                                     | Description                                                                                                                          |
         +===============================================+======================================================================================================================================+
         | volume.beta.kubernetes.io/storage-class       | Storage class, which must be the same as that of the existing PV.                                                                    |
         +-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
         | volume.beta.kubernetes.io/storage-provisioner | The field must be set to **flexvolume-huawei.com/fuxivol**.                                                                          |
         +-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
         | failure-domain.beta.kubernetes.io/region      | Region where the cluster is located.                                                                                                 |
         |                                               |                                                                                                                                      |
         |                                               | For details about the value of **region**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__. |
         +-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
         | failure-domain.beta.kubernetes.io/zone        | AZ where the EVS volume is created. It must be the same as the AZ planned for the workload.                                          |
         |                                               |                                                                                                                                      |
         |                                               | For details about the value of **zone**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.   |
         +-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
         | storage                                       | Requested capacity in the PVC, in Gi.                                                                                                |
         |                                               |                                                                                                                                      |
         |                                               | The value must be the same as the storage size of the existing PV.                                                                   |
         +-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
         | volumeName                                    | Name of the PV.                                                                                                                      |
         +-----------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+

#. Create the PV.

   **kubectl create -f pv-evs-example.yaml**

#. Create the PVC.

   **kubectl create -f pvc-evs-example.yaml**

   After the operation is successful, choose **Resource Management** > **Storage** to view the created PVC. You can also view the EVS disk by name on the EVS console.

#. (Optional) Add the metadata associated with the cluster to ensure that the EVS disk associated with the mounted static PV is not deleted when the node or cluster is deleted.

   .. caution::

      If you skip this step in this example or when creating a static PV or PVC, ensure that the EVS disk associated with the static PV has been unbound from the node before you delete the node.

   a. .. _cce_01_0313__li6891526204113:

      Obtain the tenant token. For details, see `Obtaining a User Token <https://docs.otc.t-systems.com/en-us/api/apiug/apig-en-api-180328003.html>`__.

   b. .. _cce_01_0313__li17017349418:

      Obtain the EVS access address **EVS_ENDPOINT**. For details, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.

   c. Add the metadata associated with the cluster to the EVS disk backing the static PV.

      .. code-block::

         curl -X POST ${EVS_ENDPOINT}/v2/${project_id}/volumes/${volume_id}/metadata --insecure \
             -d '{"metadata":{"cluster_id": "${cluster_id}", "namespace": "${pvc_namespace}"}}' \
             -H 'Accept:application/json' -H 'Content-Type:application/json;charset=utf8' \
             -H 'X-Auth-Token:${TOKEN}'

      .. table:: **Table 7** Key parameters

         +---------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | Parameter     | Description                                                                                                                                                                                                                                            |
         +===============+========================================================================================================================================================================================================================================================+
         | EVS_ENDPOINT  | EVS access address. Set this parameter to the value obtained in :ref:`6.b <cce_01_0313__li17017349418>`.                                                                                                                                               |
         +---------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | project_id    | Project ID. You can click the login user in the upper right corner of the console page, select **My Credentials** from the drop-down list, and view the project ID on the **Projects** tab page.                                                       |
         +---------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | volume_id     | ID of the associated EVS disk. Set this parameter to **volume_id** of the static PV to be created. You can also log in to the EVS console, click the name of the EVS disk to be imported, and obtain the ID from **Summary** on the disk details page. |
         +---------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | cluster_id    | ID of the cluster where the EVS PV is to be created. On the CCE console, choose **Resource Management** > **Clusters**. Click the name of the cluster to be associated. On the cluster details page, obtain the cluster ID.                            |
         +---------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | pvc_namespace | Namespace where the PVC is to be bound.                                                                                                                                                                                                                |
         +---------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
         | TOKEN         | User token. Set this parameter to the value obtained in :ref:`6.a <cce_01_0313__li6891526204113>`.                                                                                                                                                     |
         +---------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

      For example, run the following commands:

      .. code-block::

         curl -X POST https://evs.eu-de.otc.t-systems.com:443/v2/060576866680d5762f52c0150e726aa7/volumes/69c9619d-174c-4c41-837e-31b892604e14/metadata --insecure \
             -d '{"metadata":{"cluster_id": "71e8277e-80c7-11ea-925c-0255ac100442", "namespace": "default"}}' \
             -H 'Accept:application/json' -H 'Content-Type:application/json;charset=utf8' \
             -H 'X-Auth-Token:MIIPe******IsIm1ldG

      After the request is executed, run the following commands to check whether the EVS disk has been associated with the metadata of the cluster:

      .. code-block::

         curl -X GET ${EVS_ENDPOINT}/v2/${project_id}/volumes/${volume_id}/metadata --insecure \
             -H 'X-Auth-Token:${TOKEN}'

      For example, run the following commands:

      .. code-block::

         curl -X GET https://evs.eu-de.otc.t-systems.com/v2/060576866680d5762f52c0150e726aa7/volumes/69c9619d-174c-4c41-837e-31b892604e14/metadata --insecure \
             -H 'X-Auth-Token:MIIPeAYJ***9t1c31ASaQ=='

      The command output displays the current metadata of the EVS disk.

      .. code-block::

         {
             "metadata": {
                 "namespace": "default",
                 "cluster_id": "71e8277e-80c7-11ea-925c-0255ac100442",
                 "hw:passthrough": "true"
             }
         }
