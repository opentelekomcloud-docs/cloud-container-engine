:original_name: cce_10_0626.html

.. _cce_10_0626:

Configuring SFS Turbo Mount Options
===================================

This section describes how to configure SFS Turbo mount options. For SFS Turbo, you can only set mount options in a PV and bind the PV by creating a PVC.

Prerequisites
-------------

The :ref:`CCE Container Storage (Everest) <cce_10_0066>` version must be **1.2.8 or later**. This add-on identifies the mount options and transfers them to the underlying storage resources. The parameter settings take effect only if the underlying storage resources support the specified options.

Notes and Constraints
---------------------

-  Mount options cannot be configured for Kata containers.
-  Due to the restrictions of the NFS protocol, if an SFS volume is mounted to a node for multiple times, link-related mounting parameters (such as **timeo**) take effect only when the SFS volume is mounted for the first time by default. For example, if the same SFS file system is mounted to multiple pods running on a node, the mounting parameter set later does not overwrite the existing parameter value. If you want to configure different mounting parameters in the preceding scenario, additionally configure the **nosharecache** parameter.

.. _cce_10_0626__section14888047833:

SFS Turbo Mount Options
-----------------------

The Everest add-on in CCE presets the options described in :ref:`Table 1 <cce_10_0626__table128754351546>` for mounting SFS Turbo volumes.

.. _cce_10_0626__table128754351546:

.. table:: **Table 1** SFS Turbo mount options

   +-------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter               | Value                 | Description                                                                                                                                                                                                                                                                                                                                                                                                 |
   +=========================+=======================+=============================================================================================================================================================================================================================================================================================================================================================================================================+
   | vers                    | 3                     | File system version. Currently, only NFSv3 is supported. Value: **3**                                                                                                                                                                                                                                                                                                                                       |
   +-------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | nolock                  | Blank                 | Whether to lock files on the server using the NLM protocol. If **nolock** is selected, the lock is valid for applications on one host. For applications on another host, the lock is invalid.                                                                                                                                                                                                               |
   +-------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | timeo                   | 600                   | Waiting time before the NFS client retransmits a request. The unit is 0.1 seconds. Recommended value: **600**                                                                                                                                                                                                                                                                                               |
   +-------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | hard/soft               | Blank                 | Mount mode.                                                                                                                                                                                                                                                                                                                                                                                                 |
   |                         |                       |                                                                                                                                                                                                                                                                                                                                                                                                             |
   |                         |                       | -  **hard**: If the NFS request times out, the client keeps resending the request until the request is successful.                                                                                                                                                                                                                                                                                          |
   |                         |                       | -  **soft**: If the NFS request times out, the client returns an error to the invoking program.                                                                                                                                                                                                                                                                                                             |
   |                         |                       |                                                                                                                                                                                                                                                                                                                                                                                                             |
   |                         |                       | The default value is **hard**.                                                                                                                                                                                                                                                                                                                                                                              |
   +-------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | sharecache/nosharecache | Blank                 | How the data cache and attribute cache are shared when one file system is concurrently mounted to different clients. If this parameter is set to **sharecache**, the caches are shared between the mountings. If this parameter is set to **nosharecache**, the caches are not shared, and one cache is configured for each client mounting. The default value is **sharecache**.                           |
   |                         |                       |                                                                                                                                                                                                                                                                                                                                                                                                             |
   |                         |                       | .. note::                                                                                                                                                                                                                                                                                                                                                                                                   |
   |                         |                       |                                                                                                                                                                                                                                                                                                                                                                                                             |
   |                         |                       |    The **nosharecache** setting will affect the performance. The mounting information must be obtained for each mounting, which increases the communication overhead with the NFS server and the memory consumption of the NFS clients. In addition, the **nosharecache** setting on the NFS clients may lead to inconsistent caches. Determine whether to use **nosharecache** based on site requirements. |
   +-------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

You can set other mount options if needed. For details, see `Mounting an NFS File System to ECSs (Linux) <https://docs.otc.t-systems.com/en-us/usermanual/sfs/en-us_topic_0034428728.html>`__.

Configuring Mount Options in a PV
---------------------------------

You can use the **mountOptions** field to configure mount options in a PV. The options you can configure in **mountOptions** are listed in :ref:`SFS Turbo Mount Options <cce_10_0626__section14888047833>`.

#. Use kubectl to access the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Configure mount options in a PV. Example:

   .. code-block::

      apiVersion: v1
      kind: PersistentVolume
      metadata:
        annotations:
          pv.kubernetes.io/provisioned-by: everest-csi-provisioner
        name: pv-sfsturbo    # PV name
      spec:
        accessModes:
        - ReadWriteMany      # Access mode. The value must be ReadWriteMany for SFS Turbo.
        capacity:
          storage: 500Gi       # SFS Turbo volume capacity
        csi:
          driver: sfsturbo.csi.everest.io    # Dependent storage driver for the mounting
          fsType: nfs
          volumeHandle: {your_volume_id}   # SFS Turbo volume ID
          volumeAttributes:
            everest.io/share-export-location: {your_location}   # Shared path of the SFS Turbo volume

            storage.kubernetes.io/csiProvisionerIdentity: everest-csi-provisioner
        persistentVolumeReclaimPolicy: Retain    # Reclaim policy
        storageClassName: csi-sfsturbo           # Storage class name of the SFS Turbo file system
        mountOptions:                            # Mount options
        - vers=3
        - nolock
        - timeo=600
        - hard

#. After a PV is created, you can create a PVC and bind it to the PV, and then mount the PV to the container in the workload. For details, see :ref:`Using an Existing SFS Turbo File System Through a Static PV <cce_10_0625>`.

#. Check whether the mount options take effect.

   In this example, the PVC is mounted to the workload that uses the **nginx:latest** image. You can run the **mount -l** command to check whether the mount options take effect.

   a. View the pod to which the SFS Turbo volume has been mounted. In this example, the workload name is **web-sfsturbo**.

      .. code-block::

         kubectl get pod | grep web-sfsturbo

      Command output:

      .. code-block::

         web-sfsturbo-***   1/1     Running   0             23m

   b. Run the following command to check the mount options (**web-sfsturbo-\**\*** is an example pod):

      .. code-block::

         kubectl exec -it web-sfsturbo-*** -- mount -l | grep nfs

      If the mounting information in the command output is consistent with the configured mount options, the mount options have been configured.

      .. code-block::

         <Your mount path> on /data type nfs (rw,relatime,vers=3,rsize=1048576,wsize=1048576,namlen=255,hard,nolock,noresvport,proto=tcp,timeo=600,retrans=2,sec=sys,mountaddr=**.**.**.**,mountvers=3,mountport=20048,mountproto=tcp,local_lock=all,addr=**.**.**.**)
