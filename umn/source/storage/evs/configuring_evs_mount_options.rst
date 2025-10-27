:original_name: cce_10_1012.html

.. _cce_10_1012:

Configuring EVS Mount Options
=============================

Mount options are parameters passed to the kernel during file system mounting. They control the access mode, performance optimization, and security policies of storage devices (such as EVS, SFS, and OBS). CCE allows you to configure mount options in PVs and StorageClasses.

-  Configuring mount options in a PV applies only to that specific PV. This method is used when custom mount parameters are needed for a particular storage volume.
-  Configuring mount options in a StorageClass applies to all PVs created using that StorageClass. This method is used for centrally configuring mount options for EVS disks.

Prerequisites
-------------

The :ref:`CCE Container Storage (Everest) <cce_10_0066>` version must be **2.4.153 or later**. This add-on identifies the mount options and transfers them to the underlying storage resources. The parameter settings take effect only if the underlying storage resources support the specified options.

Notes and Constraints
---------------------

-  Mount options cannot be configured for secure containers.

.. _cce_10_1012__section1254912109811:

EVS Mount Options
-----------------

CCE supports the mount options listed in the following table.

.. table:: **Table 1** Mount options

   +------------------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                    | Value                 | Description                                                                                                                                                                                                                |
   +==============================+=======================+============================================================================================================================================================================================================================+
   | noatime/strictatime/relatime | Blank                 | Specify how file access time (atime) is updated. You can choose one of the following options:                                                                                                                              |
   |                              |                       |                                                                                                                                                                                                                            |
   |                              |                       | -  **noatime**: atime is not updated. This option provides the highest performance but completely loses atime information. It is suitable for high-concurrency read scenarios, such as databases and static file services. |
   |                              |                       | -  **relatime** (default): atime is updated only if it is earlier than mtime or if it has not been updated for more than 24 hours. This option has low overhead.                                                           |
   |                              |                       | -  **strictatime**: atime is updated every time a file is read. This option is the most accurate but has the worst performance. It is suitable for scenarios where access time needs to be strictly recorded.              |
   +------------------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Configuring Mount Options
-------------------------

You can configure mount options using one of the following methods.

Configuring Mount Options in a PV
---------------------------------

You can use **mountOptions** to configure mount options in a PV. This configuration applies only to that specific PV. For details about **mountOptions**, see :ref:`EVS Mount Options <cce_10_1012__section1254912109811>`.

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Configure mount options in a PV. Example:

   .. code-block::

      apiVersion: v1
      kind: PersistentVolume
      metadata:
        annotations:
          pv.kubernetes.io/provisioned-by: everest-csi-provisioner
        name: pv-disk  # PV name
        labels:
          failure-domain.beta.kubernetes.io/region:
          failure-domain.beta.kubernetes.io/zone:
      spec:
        accessModes:
          - ReadWriteOnce
        capacity:
          storage: 10Gi # Storage capacity
        csi:
          driver: disk.csi.everest.io
          fsType: ext4
          volumeHandle: {your_volume_id}   # EVS volume ID
          volumeAttributes:
            storage.kubernetes.io/csiProvisionerIdentity: everest-csi-provisioner
            everest.io/disk-mode: SCSI
            everest.io/disk-volume-type: SATA
            everest.io/enterprise-project-id: '0'
        persistentVolumeReclaimPolicy: Retain
        storageClassName: csi-disk
        mountOptions: # Mount options
          - strictatime

#. After creating a PV, you can create a PVC, bind it to the PV, and mount the PV to the containers in the workload. For details, see :ref:`Using an Existing EVS Disk Through a Static PV <cce_10_0614>`.

#. Check whether the mount options are effective. In this example, the PVC is mounted to a workload using the **nginx:latest** image, with the mount path **/testdir** in the container. To verify the settings, do as follows:

   a. Check the name of the pod to which the PVC has been mounted. In this example, the workload name is **web-disk**.

      .. code-block::

         kubectl get pod | grep web-disk

      Information similar to the following is displayed:

      .. code-block::

         web-disk-xxx     1/1     Running   0             23m

   b. Run the following command to access the pod:

      .. code-block::

         kubectl exec -it web-disk-xxx -- bash

   c. Run the following command to access the mount path and obtain a test file:

      .. code-block::

         cd /testdir
         echo 'ok' > testfile

   d. Run the command repeatedly and check the **Access** value in the output. If the **Access** value changes, the **strictatime** mount option is effective.

      .. code-block::

         stat testfile && cat testfile > /dev/null

Configuring Mount Options in a StorageClass
-------------------------------------------

You can also use **mountOptions** to configure mount options in a StorageClass. This configuration applies to all PVs created using this StorageClass. For details about **mountOptions**, see :ref:`EVS Mount Options <cce_10_1012__section1254912109811>`.

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Create a custom StorageClass. Example:

   .. code-block::

      allowVolumeExpansion: true
      apiVersion: storage.k8s.io/v1
      kind: StorageClass
      metadata:
        name: csi-disk # The StorageClass name
      mountOptions: # Mount options
      - strictatime
      parameters:
        csi.storage.k8s.io/csi-driver-name: disk.csi.everest.io
        csi.storage.k8s.io/fstype: ext4
        everest.io/disk-volume-type: SAS
        everest.io/passthrough: "true"
      provisioner: everest-csi-provisioner
      reclaimPolicy: Delete
      volumeBindingMode: Immediate

#. After the StorageClass is configured, you can use it to create a PVC. By default, dynamically created PVs inherit the mount options configured in the StorageClass. For details, see :ref:`Using an EVS Disk Through a Dynamic PV <cce_10_0615>`.

#. Check whether the mount options are effective. In this example, the PVC is mounted to a workload using the **nginx:latest** image, with the mount path **/testdir** in the container. To verify the settings, do as follows:

   a. Check the name of the pod to which the PVC has been mounted. In this example, the workload name is **web-disk**.

      .. code-block::

         kubectl get pod | grep web-disk

      Information similar to the following is displayed:

      .. code-block::

         web-disk-xxx     1/1     Running   0             23m

   b. Run the following command to access the pod:

      .. code-block::

         kubectl exec -it web-disk-xxx -- bash

   c. Run the following command to access the mount path and obtain a test file:

      .. code-block::

         cd /testdir
         echo 'ok' > testfile

   d. Run the command repeatedly and check the **Access** value in the output. If the **Access** value changes, the **strictatime** mount option is effective.

      .. code-block::

         stat testfile && cat testfile > /dev/null
