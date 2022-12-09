:original_name: cce_01_0337.html

.. _cce_01_0337:

Setting Mount Options
=====================

Scenario
--------

You can mount cloud storage volumes to your containers and use these volumes as local directories.

This section describes how to set mount options when mounting SFS and OBS volumes. You can set mount options in a PV and bind the PV to a PVC. Alternatively, set mount options in a StorageClass and use the StorageClass to create a PVC. In this way, PVs can be dynamically created and inherit mount options configured in the StorageClass by default.

.. _cce_01_0337__en-us_topic_0000001199021188_section14888047833:

SFS Volume Mount Options
------------------------

The everest add-on in CCE presets the options described in :ref:`Table 1 <cce_01_0337__en-us_topic_0000001199021188_table128754351546>` for mounting SFS volumes. You can set other mount options if needed. For details, see `Mounting an NFS File System to ECSs (Linux) <https://docs.otc.t-systems.com/en-us/usermanual/sfs/en-us_topic_0034428728.html>`__.

.. _cce_01_0337__en-us_topic_0000001199021188_table128754351546:

.. table:: **Table 1** Preset mount options for SFS volumes

   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Option                            | Description                                                                                                                                                                                   |
   +===================================+===============================================================================================================================================================================================+
   | vers=3                            | File system version. Currently, only NFSv3 is supported, Value: **3**                                                                                                                         |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | nolock                            | Whether to lock files on the server using the NLM protocol. If **nolock** is selected, the lock is valid for applications on one host. For applications on another host, the lock is invalid. |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | timeo=600                         | Waiting time before the NFS client retransmits a request. The unit is 0.1 seconds. Recommended value: **600**                                                                                 |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | hard/soft                         | Mounting mode.                                                                                                                                                                                |
   |                                   |                                                                                                                                                                                               |
   |                                   | -  **hard**: If the NFS request times out, the client keeps resending the request until the request is successful.                                                                            |
   |                                   | -  **soft**: If the NFS request times out, the client returns an error to the invoking program.                                                                                               |
   |                                   |                                                                                                                                                                                               |
   |                                   | The default value is **hard**.                                                                                                                                                                |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_01_0337__en-us_topic_0000001199021188_section1254912109811:

OBS Volume Mount Options
------------------------

When mounting file storage, the everest add-on presets the options described in :ref:`Table 2 <cce_01_0337__en-us_topic_0000001199021188_table1688593020213>` and :ref:`Table 3 <cce_01_0337__en-us_topic_0000001199021188_table9886123010217>` by default. The options in :ref:`Table 2 <cce_01_0337__en-us_topic_0000001199021188_table1688593020213>` are mandatory.

.. _cce_01_0337__en-us_topic_0000001199021188_table1688593020213:

.. table:: **Table 2** Mandatory mount options configured by default

   +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | Option                            | Description                                                                                                                              |
   +===================================+==========================================================================================================================================+
   | use_ino                           | If enabled, obsfs allocates the **inode** number. Enabled by default in read/write mode.                                                 |
   +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | big_writes                        | If configured, the maximum size of the cache can be modified.                                                                            |
   +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | nonempty                          | Allows non-empty mount paths.                                                                                                            |
   +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | allow_other                       | Allows other users to access the parallel file system.                                                                                   |
   +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | no_check_certificate              | Disables server certificate verification.                                                                                                |
   +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | enable_noobj_cache                | Enables cache entries for objects that do not exist, which can improve performance. Enabled by default in object bucket read/write mode. |
   |                                   |                                                                                                                                          |
   |                                   | **This option is no longer set by default since everest 1.2.40.**                                                                        |
   +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | sigv2                             | Specifies the signature version. Used by default in object buckets.                                                                      |
   +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_01_0337__en-us_topic_0000001199021188_table9886123010217:

.. table:: **Table 3** Optional mount options configured by default

   +-----------------------+--------------------------------------------------------------------------------------------------------------------+
   | Option                | Description                                                                                                        |
   +=======================+====================================================================================================================+
   | max_write=131072      | If specified, obsfs allocates the **inode** number. Enabled by default in read/write mode.                         |
   +-----------------------+--------------------------------------------------------------------------------------------------------------------+
   | ssl_verify_hostname=0 | Disables verifying the SSL certificate based on the host name.                                                     |
   +-----------------------+--------------------------------------------------------------------------------------------------------------------+
   | max_background=100    | Allows setting the maximum number of waiting requests in the background. Used by default in parallel file systems. |
   +-----------------------+--------------------------------------------------------------------------------------------------------------------+
   | public_bucket=1       | If set to **1**, public buckets are mounted anonymously. Enabled by default in object bucket read/write mode.      |
   +-----------------------+--------------------------------------------------------------------------------------------------------------------+

You can log in to the node to which the pod is scheduled and view all mount options used for mounting the OBS volume in the process details.

-  Object bucket: ps -ef \| grep s3fs

   .. code-block::

      root     22142     1  0 Jun03 ?        00:00:00 /usr/bin/s3fs pvc-82fe2cbe-3838-43a2-8afb-f994e402fb9d /mnt/paas/kubernetes/kubelet/pods/0b13ff68-4c8e-4a1c-b15c-724fd4d64389/volumes/kubernetes.io~csi/pvc-82fe2cbe-3838-43a2-8afb-f994e402fb9d/mount -o url=https://{{endpoint}}:443 -o endpoint=xxxxxx -o passwd_file=/opt/everest-host-connector/1622707954357702943_obstmpcred/pvc-82fe2cbe-3838-43a2-8afb-f994e402fb9d -o nonempty -o big_writes -o enable_noobj_cache -o sigv2 -o allow_other -o no_check_certificate -o ssl_verify_hostname=0 -o max_write=131072 -o multipart_size=20 -o umask=0

-  Parallel file system: ps -ef \| grep obsfs

   .. code-block::

      root      1355     1  0 Jun03 ?        00:03:16 /usr/bin/obsfs pvc-86720bb9-5aa8-4cde-9231-5253994f8468 /mnt/paas/kubernetes/kubelet/pods/c959a91d-eced-4b41-91c6-96cbd65324f9/volumes/kubernetes.io~csi/pvc-86720bb9-5aa8-4cde-9231-5253994f8468/mount -o url=https://{{endpoint}}:443 -o endpoint=xxxxxx -o passwd_file=/opt/everest-host-connector/1622714415305160399_obstmpcred/pvc-86720bb9-5aa8-4cde-9231-5253994f8468 -o allow_other -o nonempty -o big_writes -o use_ino -o no_check_certificate -o ssl_verify_hostname=0 -o umask=0027 -o max_write=131072 -o max_background=100 -o uid=10000 -o gid=10000

Prerequisites
-------------

-  The everest add-on version must be **1.2.8 or later**.
-  The add-on identifies the mount options and transfers them to the underlying storage resources, which determine whether the specified options are valid.

Notes and Constraints
---------------------

Mount options cannot be configured for secure containers.

Setting Mount Options in a PV
-----------------------------

You can use the **mountOptions** field to set mount options in a PV. The options you can configure in **mountOptions** are listed in :ref:`SFS Volume Mount Options <cce_01_0337__en-us_topic_0000001199021188_section14888047833>` and :ref:`OBS Volume Mount Options <cce_01_0337__en-us_topic_0000001199021188_section1254912109811>`.

.. code-block::

   apiVersion: v1
   kind: PersistentVolume
   metadata:
     name: pv-obs-example
     annotations:
       pv.kubernetes.io/provisioned-by: everest-csi-provisioner
   spec:
     mountOptions:
     - umask=0027
     - uid=10000
     - gid=10000
     accessModes:
     - ReadWriteMany
     capacity:
       storage: 1Gi
     claimRef:
       apiVersion: v1
       kind: PersistentVolumeClaim
       name: pvc-obs-example
       namespace: default
     csi:
       driver: obs.csi.everest.io
       fsType: obsfs
       volumeAttributes:
         everest.io/obs-volume-type: STANDARD
         everest.io/region: eu-de
         storage.kubernetes.io/csiProvisionerIdentity: everest-csi-provisioner
       volumeHandle: obs-normal-static-pv
     persistentVolumeReclaimPolicy: Delete
     storageClassName: csi-obs

After a PV is created, you can create a PVC and bind it to the PV, and then mount the PV to the container in the workload.

Setting Mount Options in a StorageClass
---------------------------------------

You can use the **mountOptions** field to set mount options in a StorageClass. The options you can configure in **mountOptions** are listed in :ref:`SFS Volume Mount Options <cce_01_0337__en-us_topic_0000001199021188_section14888047833>` and :ref:`OBS Volume Mount Options <cce_01_0337__en-us_topic_0000001199021188_section1254912109811>`.

.. code-block::

   apiVersion: storage.k8s.io/v1
   kind: StorageClass
   metadata:
     name: csi-obs-mount-option
   mountOptions:
   - umask=0027
   - uid=10000
   - gid=10000
   parameters:
     csi.storage.k8s.io/csi-driver-name: obs.csi.everest.io
     csi.storage.k8s.io/fstype: s3fs
     everest.io/obs-volume-type: STANDARD
   provisioner: everest-csi-provisioner
   reclaimPolicy: Delete
   volumeBindingMode: Immediate

After the StorageClass is configured, you can use it to create a PVC. By default, the dynamically created PVs inherit the mount options set in the StorageClass.
