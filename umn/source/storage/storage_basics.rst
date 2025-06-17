:original_name: cce_10_0378.html

.. _cce_10_0378:

Storage Basics
==============

Volumes
-------

On-disk files in a container are ephemeral, which presents the following problems to important applications running in the container:

#. When a container is rebuilt, files in the container will be lost.
#. When multiple containers run in a pod at the same time, files need to be shared among the containers.

Kubernetes volumes resolve both of these problems. Volumes, as part of a pod, cannot be created independently and can only be defined in pods. All containers in a pod can access its volumes, but the volumes must have been mounted to any directory in a container.

The following figure shows how a storage volume is used between containers in a pod.

|image1|

The basic principles for using volumes are as follows:

-  Multiple volumes can be mounted to a pod. However, do not mount too many volumes to a pod.
-  Multiple types of volumes can be mounted to a pod.
-  Each volume mounted to a pod can be shared among containers in the pod.
-  You are advised to use PVCs and PVs to mount volumes for Kubernetes.

.. note::

   The lifecycle of a volume is the same as that of the pod to which the volume is mounted. When the pod is deleted, the volume is also deleted. However, files in the volume may outlive the volume, depending on the volume type.

Kubernetes provides various volume types, which can be classified as in-tree and out-of-tree.

+-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Volume Classification             | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
+===================================+===============================================================================================================================================================================================================================================================================================================================================================================================================================================================================+
| In-tree                           | Maintained through the Kubernetes code repository and built, edited, and released with Kubernetes binary files. Kubernetes does not accept this volume type anymore.                                                                                                                                                                                                                                                                                                          |
|                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|                                   | Kubernetes-native volumes such as hostPath, emptyDir, Secret, and ConfigMap are all the in-tree type.                                                                                                                                                                                                                                                                                                                                                                         |
|                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|                                   | PVCs are a special in-tree volume. Kubernetes uses this type of volume to convert from in-tree to out-of-tree. PVCs allow you to request for PVs created using the underlying storage resources provided by different storage vendors.                                                                                                                                                                                                                                        |
+-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Out-of-tree                       | Out-of-tree volumes include container storage interfaces (CSIs) and FlexVolumes (deprecated). Storage vendors only need to comply with certain specifications to create custom storage add-ons and PVs that can be used by Kubernetes, without adding add-on source code to the Kubernetes code repository. Cloud storage such as SFS and OBS is used by installing storage drivers in a cluster. You need to create PVs in the cluster and mount the PVs to pods using PVCs. |
+-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

PV and PVC
----------

Kubernetes provides PersistentVolumes (PVs) and PersistentVolumeClaims (PVCs) to abstract details of how storage is provided from how it is consumed. You can request specific size of storage when needed, just like pods can request specific levels of resources (CPU and memory).

-  PV: describes a persistent storage volume in a cluster. A PV is a cluster-level resource just like a node. It applies to the entire Kubernetes cluster. A PV has a lifecycle independent of any individual Pod that uses the PV.
-  PVC: describes a request for storage by a user. When configuring storage for an application, claim a storage request (that is, PVC). Kubernetes selects a PV that best meets the request and binds the PV to the PVC. A PVC to PV binding is a one-to-one mapping. When creating a PVC, describe the attributes of the requested persistent storage, such as the storage size and read/write permission.

You can bind PVCs to PVs in a pod so that the pod can use storage resources. The following figure shows the relationship between PVs and PVCs.


.. figure:: /_static/images/en-us_image_0000002218820410.png
   :alt: **Figure 1** PVC-to-PV binding

   **Figure 1** PVC-to-PV binding

.. _cce_10_0378__section79711433131110:

CSI
---

CSI is a standard for container storage interfaces and a storage plugin implementation solution recommended by the Kubernetes community. :ref:`CCE Container Storage (Everest) <cce_10_0066>` is a storage add-on developed based on CSI. It provides different types of persistent storage for containers.

.. _cce_10_0378__section43881411172418:

Volume Access Modes
-------------------

Storage volumes can be mounted to the host system only in the mode supported by underlying storage resources. For example, a file storage system can be read and written by multiple nodes, but an EVS disk can be read and written by only one node.

-  **ReadWriteOnce**: A storage volume can be mounted to a single node in read-write mode.
-  **ReadWriteMany**: A storage volume can be mounted to multiple nodes in read-write mode.

.. table:: **Table 1** Access modes supported by storage volumes

   =========== ============= =============
   Volume Type ReadWriteOnce ReadWriteMany
   =========== ============= =============
   EVS         Y             x
   SFS         x             Y
   OBS         x             Y
   SFS Turbo   x             Y
   Local PV    Y             x
   =========== ============= =============

Mounting a Storage Volume
-------------------------

You can mount volumes in the following ways:

Use PVs to describe existing storage resources, and then create PVCs to use the storage resources in pods. You can also use the dynamic creation mode. That is, specify the :ref:`StorageClass <cce_10_0380>` when creating a PVC and use the provisioner in the StorageClass to automatically create a PV and bind the PV to the PVC.

.. table:: **Table 2** Modes of mounting volumes

   +-----------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------+--------------------------------+
   | Mount Mode                                                            | Description                                                                                                                                                                                                                                                                                                                                                                            | Supported Volume Type       | Other Constraints              |
   +=======================================================================+========================================================================================================================================================================================================================================================================================================================================================================================+=============================+================================+
   | Statically creating storage volume (using existing storage)           | Use existing storage (such as EVS disks and SFS file systems) to create PVs and mount the PVs to the workload through PVCs. Kubernetes binds PVCs to the matching PVs so that workloads can access storage services.                                                                                                                                                                   | All volumes                 | None                           |
   +-----------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------+--------------------------------+
   | Dynamically creating storage volumes (automatically creating storage) | Specify a :ref:`StorageClass <cce_10_0380>` for a PVC. The storage provisioner creates underlying storage media as required to automatically create PVs and directly bind the PV to the PVC.                                                                                                                                                                                           | EVS, OBS, SFS, and local PV | None                           |
   +-----------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------+--------------------------------+
   | Dynamic mounting (VolumeClaimTemplate)                                | Achieved by using the `volumeClaimTemplates <https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/#volume-claim-templates>`__ field and depends on the dynamic PV creation capability of StorageClass. In this mode, each pod is associated with a unique PVC and PV. After a pod is rescheduled, the original data can still be mounted to it based on the PVC name. | EVS and local PV            | Supported only by StatefulSets |
   +-----------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------+--------------------------------+

.. _cce_10_0378__section19999142414413:

PV Reclaim Policy
-----------------

A PV reclaim policy is used to delete or reclaim underlying volumes when a PVC is deleted. The value can be **Delete** or **Retain**.

-  **Delete**: Deleting a PVC will remove the PV from Kubernetes, and the associated underlying storage assets will also be removed from the external infrastructure.

-  **Retain**: When a PVC is deleted, both the PV and underlying storage resources will be retained. You need to manually delete these resources. After the PVC is deleted, the PV is in the **Released** state and cannot be bound to a PVC again.

   You can manually delete and reclaim volumes by performing the following operations:

   #. Delete the PV.
   #. Clear data on the associated underlying storage resources as required.
   #. Delete the associated underlying storage resources.

   To reuse the underlying storage resources, create a PV.

CCE also allows you to delete a PVC without deleting underlying storage resources. This function can be achieved only by using a YAML file: Set the PV reclaim policy to **Delete** and add **everest.io/reclaim-policy: retain-volume-only** to **annotations**. In this way, when the PVC is deleted, the PV is deleted, but the underlying storage resources are retained.

The following YAML file takes EVS as an example:

.. code-block::

   apiVersion: v1
   kind: PersistentVolumeClaim
   metadata:
     name: test
     namespace: default
     annotations:
       volume.beta.kubernetes.io/storage-provisioner: everest-csi-provisioner
       everest.io/disk-volume-type: SAS
     labels:
       failure-domain.beta.kubernetes.io/region: <your_region>   # Region of the node where the application is to be deployed
       failure-domain.beta.kubernetes.io/zone: <your_zone>       # AZ of the node where the application is to be deployed
   spec:
     accessModes:
       - ReadWriteOnce
     resources:
       requests:
         storage: 10Gi
     storageClassName: csi-disk
     volumeName: pv-evs-test

   ---
   apiVersion: v1
   kind: PersistentVolume
   metadata:
     annotations:
       pv.kubernetes.io/provisioned-by: everest-csi-provisioner
       everest.io/reclaim-policy: retain-volume-only
     name: pv-evs-test
     labels:
       failure-domain.beta.kubernetes.io/region: <your_region>   # Region of the node where the application is to be deployed
       failure-domain.beta.kubernetes.io/zone: <your_zone>       # AZ of the node where the application is to be deployed
   spec:
     accessModes:
       - ReadWriteOnce
     capacity:
       storage: 10Gi
     csi:
       driver: disk.csi.everest.io
       fsType: ext4
       volumeHandle: 2af98016-6082-4ad6-bedc-1a9c673aef20
       volumeAttributes:
         storage.kubernetes.io/csiProvisionerIdentity: everest-csi-provisioner
         everest.io/disk-mode: SCSI
         everest.io/disk-volume-type: SAS
     persistentVolumeReclaimPolicy: Delete
     storageClassName: csi-disk

Documentation
-------------

-  For more information about Kubernetes storage, see `Storage <https://kubernetes.io/docs/concepts/storage/>`__.
-  For more information about CCE container storage, see :ref:`Overview <cce_10_0307>`.

.. |image1| image:: /_static/images/en-us_image_0000002218660566.png
