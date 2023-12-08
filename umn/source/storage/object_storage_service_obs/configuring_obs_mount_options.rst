:original_name: cce_10_0631.html

.. _cce_10_0631:

Configuring OBS Mount Options
=============================

This section describes how to configure OBS volume mount options. You can configure mount options in a PV and bind the PV to a PVC. Alternatively, configure mount options in a StorageClass and use the StorageClass to create a PVC. In this way, PVs can be dynamically created and inherit mount options configured in the StorageClass by default.

Prerequisites
-------------

The everest add-on version must be **1.2.8 or later**. The add-on identifies the mount options and transfers them to the underlying storage resources, which determine whether the specified options are valid.

Constraints
-----------

Mount options cannot be configured for Kata containers.

.. _cce_10_0631__section1254912109811:

OBS Mount Options
-----------------

When mounting an OBS volume, the everest add-on presets the options described in :ref:`Table 1 <cce_10_0631__table1688593020213>` and :ref:`Table 2 <cce_10_0631__table9886123010217>` by default. The options in :ref:`Table 1 <cce_10_0631__table1688593020213>` are mandatory.

.. _cce_10_0631__table1688593020213:

.. table:: **Table 1** Mandatory mount options configured by default

   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter             | Value                 | Description                                                                                                                              |
   +=======================+=======================+==========================================================================================================================================+
   | use_ino               | Leave it blank.       | If enabled, obsfs allocates the **inode** number. Enabled by default in read/write mode.                                                 |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | big_writes            | Leave it blank.       | If configured, the maximum size of the cache can be modified.                                                                            |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | nonempty              | Leave it blank.       | Allows non-empty mount paths.                                                                                                            |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | allow_other           | Leave it blank.       | Allows other users to access the parallel file system.                                                                                   |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | no_check_certificate  | Leave it blank.       | Disables server certificate verification.                                                                                                |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | enable_noobj_cache    | Leave it blank.       | Enables cache entries for objects that do not exist, which can improve performance. Enabled by default in object bucket read/write mode. |
   |                       |                       |                                                                                                                                          |
   |                       |                       | **This option is no longer configured by default since everest 1.2.40.**                                                                 |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------+
   | sigv2                 | Leave it blank.       | Specifies the signature version. Used by default in object buckets.                                                                      |
   +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_10_0631__table9886123010217:

.. table:: **Table 2** Optional mount options configured by default

   +---------------------+-----------------+--------------------------------------------------------------------------------------------------------------------+
   | Parameter           | Value           | Description                                                                                                        |
   +=====================+=================+====================================================================================================================+
   | max_write           | 131072          | This parameter is valid only when **big_writes** is configured. The recommended value is **128 KB**.               |
   +---------------------+-----------------+--------------------------------------------------------------------------------------------------------------------+
   | ssl_verify_hostname | 0               | Disables SSL certificate verification based on the host name.                                                      |
   +---------------------+-----------------+--------------------------------------------------------------------------------------------------------------------+
   | max_background      | 100             | Allows setting the maximum number of waiting requests in the background. Used by default in parallel file systems. |
   +---------------------+-----------------+--------------------------------------------------------------------------------------------------------------------+
   | public_bucket       | 1               | If set to **1**, public buckets are mounted anonymously. Enabled by default in object bucket read/write mode.      |
   +---------------------+-----------------+--------------------------------------------------------------------------------------------------------------------+
   | umask               | Leave it blank. | Mask of the configuration file permission.                                                                         |
   +---------------------+-----------------+--------------------------------------------------------------------------------------------------------------------+

Configuring Mount Options in a PV
---------------------------------

You can use the **mountOptions** field to configure mount options in a PV. The options you can configure in **mountOptions** are listed in :ref:`OBS Mount Options <cce_10_0631__section1254912109811>`.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Configure mount options in a PV. Example:

   .. code-block::

      apiVersion: v1
      kind: PersistentVolume
      metadata:
        annotations:
          pv.kubernetes.io/provisioned-by: everest-csi-provisioner
          everest.io/reclaim-policy: retain-volume-only      # (Optional) The PV is deleted while the underlying volume is retained.
        name: pv-obs       # PV name.
      spec:
        accessModes:
        - ReadWriteMany    # Access mode. The value must be ReadWriteMany for OBS.
        capacity:
          storage: 1Gi     # OBS volume capacity.
        csi:
          driver: obs.csi.everest.io        # Dependent storage driver for the mounting.
          fsType: obsfs                     # Instance type.
          volumeHandle: <your_volume_id>    # Name of the OBS volume.
          volumeAttributes:
            storage.kubernetes.io/csiProvisionerIdentity: everest-csi-provisioner
            everest.io/obs-volume-type: STANDARD
            everest.io/region: <your_region>                        # Region where the OBS volume is.

          nodePublishSecretRef:            # Custom secret of the OBS volume.
            name: <your_secret_name>       # Custom secret name.
            namespace: <your_namespace>    # Namespace of the custom secret.
        persistentVolumeReclaimPolicy: Retain    # Reclaim policy.
        storageClassName: csi-obs               # Storage class name.
        mountOptions:                            # Mount options.
        - umask=0027

#. After a PV is created, you can create a PVC and bind it to the PV, and then mount the PV to the container in the workload. For details, see :ref:`Using an Existing OBS Bucket Through a Static PV <cce_10_0379>`.

#. Check whether the mount options take effect.

   In this example, the PVC is mounted to the workload that uses the **nginx:latest** image. You can log in to the node where the pod to which the OBS volume is mounted resides and view the progress details.

   Run the following command:

   -  Object bucket: **ps -ef \| grep s3fs**

      .. code-block::

         root     22142     1  0 Jun03 ?        00:00:00 /usr/bin/s3fs {your_obs_name} /mnt/paas/kubernetes/kubelet/pods/{pod_uid}/volumes/kubernetes.io~csi/{your_pv_name}/mount -o url=https://{endpoint}:443 -o endpoint={region} -o passwd_file=/opt/everest-host-connector/***_obstmpcred/{your_obs_name} -o nonempty -o big_writes -o sigv2 -o allow_other -o no_check_certificate -o ssl_verify_hostname=0 -o umask=0027 -o max_write=131072 -o multipart_size=20

   -  Parallel file system: **ps -ef \| grep obsfs**

      .. code-block::

         root      1355     1  0 Jun03 ?        00:03:16 /usr/bin/obsfs {your_obs_name} /mnt/paas/kubernetes/kubelet/pods/{pod_uid}/volumes/kubernetes.io~csi/{your_pv_name}/mount -o url=https://{endpoint}:443 -o endpoint={region} -o passwd_file=/opt/everest-host-connector/***_obstmpcred/{your_obs_name} -o allow_other -o nonempty -o big_writes -o use_ino -o no_check_certificate -o ssl_verify_hostname=0 -o max_background=100 -o umask=0027 -o max_write=131072

Configuring Mount Options in a StorageClass
-------------------------------------------

You can use the **mountOptions** field to configure mount options in a StorageClass. The options you can configure in **mountOptions** are listed in :ref:`OBS Mount Options <cce_10_0631__section1254912109811>`.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create a customized StorageClass. Example:

   .. code-block::

      kind: StorageClass
      apiVersion: storage.k8s.io/v1
      metadata:
        name: csi-obs-mount-option
      provisioner: everest-csi-provisioner
      parameters:
        csi.storage.k8s.io/csi-driver-name: obs.csi.everest.io
        csi.storage.k8s.io/fstype: s3fs
        everest.io/obs-volume-type: STANDARD
      reclaimPolicy: Delete
      volumeBindingMode: Immediate
      mountOptions:                            # Mount options.
      - umask=0027

#. After the StorageClass is configured, you can use it to create a PVC. By default, the dynamically created PVs inherit the mount options configured in the StorageClass. For details, see :ref:`Using an OBS Bucket Through a Dynamic PV <cce_10_0630>`.

#. Check whether the mount options take effect.

   In this example, the PVC is mounted to the workload that uses the **nginx:latest** image. You can log in to the node where the pod to which the OBS volume is mounted resides and view the progress details.

   Run the following command:

   -  Object bucket: **ps -ef \| grep s3fs**

      .. code-block::

         root     22142     1  0 Jun03 ?        00:00:00 /usr/bin/s3fs {your_obs_name} /mnt/paas/kubernetes/kubelet/pods/{pod_uid}/volumes/kubernetes.io~csi/{your_pv_name}/mount -o url=https://{endpoint}:443 -o endpoint={region} -o passwd_file=/opt/everest-host-connector/***_obstmpcred/{your_obs_name} -o nonempty -o big_writes -o sigv2 -o allow_other -o no_check_certificate -o ssl_verify_hostname=0 -o umask=0027 -o max_write=131072 -o multipart_size=20

   -  Parallel file system: **ps -ef \| grep obsfs**

      .. code-block::

         root      1355     1  0 Jun03 ?        00:03:16 /usr/bin/obsfs {your_obs_name} /mnt/paas/kubernetes/kubelet/pods/{pod_uid}/volumes/kubernetes.io~csi/{your_pv_name}/mount -o url=https://{endpoint}:443 -o endpoint={region} -o passwd_file=/opt/everest-host-connector/***_obstmpcred/{your_obs_name} -o allow_other -o nonempty -o big_writes -o use_ino -o no_check_certificate -o ssl_verify_hostname=0 -o max_background=100 -o umask=0027 -o max_write=131072
