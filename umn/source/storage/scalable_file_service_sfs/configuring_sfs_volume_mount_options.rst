:original_name: cce_10_0337.html

.. _cce_10_0337:

Configuring SFS Volume Mount Options
====================================

This section describes how to configure SFS volume mount options. You can configure mount options in a PV and bind the PV to a PVC. Alternatively, configure mount options in a StorageClass and use the StorageClass to create a PVC. In this way, PVs can be dynamically created and inherit mount options configured in the StorageClass by default.

Prerequisites
-------------

The everest add-on version must be **1.2.8 or later**. The add-on identifies the mount options and transfers them to the underlying storage resources, which determine whether the specified options are valid.

Constraints
-----------

Mount options cannot be configured for Kata containers.

.. _cce_10_0337__section14888047833:

SFS Volume Mount Options
------------------------

The everest add-on in CCE presets the options described in :ref:`Table 1 <cce_10_0337__table128754351546>` for mounting SFS volumes.

.. _cce_10_0337__table128754351546:

.. table:: **Table 1** SFS volume mount options

   +-------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter               | Value                 | Description                                                                                                                                                                                   |
   +=========================+=======================+===============================================================================================================================================================================================+
   | keep-original-ownership | Leave it blank.       | Whether to retain the ownership of the file mount point. If this option is used, the everest add-on must be v1.2.63 or v2.1.2 or later.                                                       |
   |                         |                       |                                                                                                                                                                                               |
   |                         |                       | -  By default, this option is not added. and the mount point ownership is **root:root** when SFS is mounted.                                                                                  |
   |                         |                       |                                                                                                                                                                                               |
   |                         |                       | -  If this option is added, the original ownership of the file system is retained when SFS is mounted.                                                                                        |
   +-------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | vers                    | 3                     | File system version. Currently, only NFSv3 is supported. Value: **3**                                                                                                                         |
   +-------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | nolock                  | Leave it blank.       | Whether to lock files on the server using the NLM protocol. If **nolock** is selected, the lock is valid for applications on one host. For applications on another host, the lock is invalid. |
   +-------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | timeo                   | 600                   | Waiting time before the NFS client retransmits a request. The unit is 0.1 seconds. Recommended value: **600**                                                                                 |
   +-------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | hard/soft               | Leave it blank.       | Mount mode.                                                                                                                                                                                   |
   |                         |                       |                                                                                                                                                                                               |
   |                         |                       | -  **hard**: If the NFS request times out, the client keeps resending the request until the request is successful.                                                                            |
   |                         |                       | -  **soft**: If the NFS request times out, the client returns an error to the invoking program.                                                                                               |
   |                         |                       |                                                                                                                                                                                               |
   |                         |                       | The default value is **hard**.                                                                                                                                                                |
   +-------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

You can set other mount options if needed. For details, see `Mounting an NFS File System to ECSs (Linux) <https://docs.otc.t-systems.com/en-us/usermanual/sfs/en-us_topic_0034428728.html>`__.

Configuring Mount Options in a PV
---------------------------------

You can use the **mountOptions** field to configure mount options in a PV. The options you can configure in **mountOptions** are listed in :ref:`SFS Volume Mount Options <cce_10_0337__section14888047833>`.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Configure mount options in a PV. Example:

   .. code-block::

      apiVersion: v1
      kind: PersistentVolume
      metadata:
        annotations:
          pv.kubernetes.io/provisioned-by: everest-csi-provisioner
          everest.io/reclaim-policy: retain-volume-only      # (Optional) The PV is deleted while the underlying volume is retained.
        name: pv-sfs
      spec:
        accessModes:
        - ReadWriteMany      # Access mode. The value must be ReadWriteMany for SFS.
        capacity:
          storage: 1Gi     # SFS volume capacity.
        csi:
          driver: disk.csi.everest.io   # Dependent storage driver for the mounting.
          fsType: nfs
          volumeHandle: <your_volume_id>   # ID of the SFS Capacity-Oriented volume.
          volumeAttributes:
            everest.io/share-export-location: <your_location>  # Shared path of the SFS volume.
            storage.kubernetes.io/csiProvisionerIdentity: everest-csi-provisioner
        persistentVolumeReclaimPolicy: Retain    # Reclaim policy.
        storageClassName: csi-nas                # Storage class name.
        mountOptions:                            # Mount options.
        - vers=3
        - nolock
        - timeo=600
        - hard

#. After a PV is created, you can create a PVC and bind it to the PV, and then mount the PV to the container in the workload. For details, see :ref:`Using an Existing SFS File System Through a Static PV <cce_10_0619>`.

#. Check whether the mount options take effect.

   In this example, the PVC is mounted to the workload that uses the **nginx:latest** image. You can run the **mount -l** command to check whether the mount options take effect.

   a. View the pod to which the SFS volume has been mounted. In this example, the workload name is **web-sfs**.

      .. code-block::

         kubectl get pod | grep web-sfs

      Command output:

      .. code-block::

         web-sfs-***   1/1     Running   0             23m

   b. Run the following command to check the mount options (**web-sfs-**\*** is an example pod):

      .. code-block::

         kubectl exec -it web-sfs-*** -- mount -l | grep nfs

      If the mounting information in the command output is consistent with the configured mount options, the mount options are set successfully.

      .. code-block::

         <Your shared path> on /data type nfs (rw,relatime,vers=3,rsize=1048576,wsize=1048576,namlen=255,hard,nolock,noresvport,proto=tcp,timeo=600,retrans=2,sec=sys,mountaddr=**.**.**.**,mountvers=3,mountport=2050,mountproto=tcp,local_lock=all,addr=**.**.**.**)

Setting Mount Options in a StorageClass
---------------------------------------

You can use the **mountOptions** field to configure mount options in a StorageClass. The options you can configure in **mountOptions** are listed in :ref:`SFS Volume Mount Options <cce_10_0337__section14888047833>`.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create a customized StorageClass. Example:

   .. code-block::

      apiVersion: storage.k8s.io/v1
      kind: StorageClass
      metadata:
        name: csi-sfs-mount-option
      provisioner: everest-csi-provisioner
      parameters:
        csi.storage.k8s.io/csi-driver-name: nas.csi.everest.io
        csi.storage.k8s.io/fstype: nfs
      everest.io/share-access-to: <your_vpc_id> # VPC ID of the cluster.
      reclaimPolicy: Delete
      volumeBindingMode: Immediate
      mountOptions:                            # Mount options
      - vers=3
      - nolock
      - timeo=600
      - hard

#. After the StorageClass is configured, you can use it to create a PVC. By default, the dynamically created PVs inherit the mount options configured in the StorageClass. For details, see :ref:`Using an SFS File System Through a Dynamic PV <cce_10_0620>`.

#. Check whether the mount options take effect.

   In this example, the PVC is mounted to the workload that uses the **nginx:latest** image. You can run the **mount -l** command to check whether the mount options take effect.

   a. View the pod to which the SFS volume has been mounted. In this example, the workload name is **web-sfs**.

      .. code-block::

         kubectl get pod | grep web-sfs

      Command output:

      .. code-block::

         web-sfs-***   1/1     Running   0             23m

   b. Run the following command to check the mount options (**web-sfs-**\*** is an example pod):

      .. code-block::

         kubectl exec -it web-sfs-*** -- mount -l | grep nfs

      If the mounting information in the command output is consistent with the configured mount options, the mount options are set successfully.

      .. code-block::

         <Your shared path> on /data type nfs (rw,relatime,vers=3,rsize=1048576,wsize=1048576,namlen=255,hard,nolock,noresvport,proto=tcp,timeo=600,retrans=2,sec=sys,mountaddr=**.**.**.**,mountvers=3,mountport=2050,mountproto=tcp,local_lock=all,addr=**.**.**.**)
