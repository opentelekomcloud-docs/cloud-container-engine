:original_name: cce_10_0336.html

.. _cce_10_0336:

Using a Custom Access Key (AK/SK) to Mount an OBS Volume
========================================================

:ref:`CCE Container Storage (Everest) <cce_10_0066>` supports custom access keys. In this way, IAM users can use their own custom access keys to mount an OBS volume.

Prerequisites
-------------

-  The :ref:`CCE Container Storage (Everest) <cce_10_0066>` version must be 1.2.8 or later.
-  The cluster version must be 1.15.11 or later.

Constraints
-----------

-  When an OBS volume is mounted using a custom access key (AK/SK), the access key cannot be deleted or disabled. Otherwise, the service container cannot access the mounted OBS volume.
-  Custom access keys cannot be configured for secure containers.

Disabling a Global AK
---------------------

When creating an OBS volume on the console of an earlier version, you need to upload the AK/SK (global access key), which is then used by default for mounting the OBS volume. As a result, all IAM users within your account will use the same key to mount the OBS buckets, and they will have identical permissions on the buckets. However, this setting does not allow you to set different permissions for individual IAM users.

If you have uploaded the AK/SK, disable the automatic mounting of global access keys by enabling the **DISABLE_AUTO_MOUNT_SECRET** parameter in the CCE Container Storage (Everest) add-on to prevent IAM users from performing unauthorized operations. In this way, the global access keys uploaded on the console will not be used when you use OBS volumes.

.. note::

   -  Before enabling **DISABLE_AUTO_MOUNT_SECRET**, ensure that there are no OBS volumes in the cluster. Workloads using OBS volumes may fail to remount after scaling or restart due to missing access keys, which are blocked by **DISABLE_AUTO_MOUNT_SECRET**.
   -  If **DISABLE_AUTO_MOUNT_SECRET** is set to **true**, an access key must be specified when a PV or PVC is created. Otherwise, mounting the OBS volume will fail.

The following steps apply to CCE Container Storage (Everest) 2.\ *x* (2.1.42 or later):

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Add-ons**. In the right pane, find the CCE Container Storage (Everest) add-on and click **Edit**.
#. Configure the add-on parameters. Set **Prohibit Global Secret from Mounting Object Storage (disable_auto_mount_secret)** to **Yes**.
#. Click **OK**.

The following steps apply to CCE Container Storage (Everest) 1.\ *x*. (The modified settings cannot be retained during the add-on upgrades. You are advised to use the add-on of 2.\ *x*.)

#. Use kubectl to access the cluster and run the following command to modify the add-on settings:

   .. code-block::

      kubectl edit ds everest-csi-driver -nkube-system

#. Search for **disable-auto-mount-secret** and set it to **true**.

   |image1|

#. Run **:wq** to save the settings and exit. Wait until the pod is restarted.

.. _cce_10_0336__section4633162355911:

Obtaining an Access Key
-----------------------

#. Access the **My Credentials** page.
#. In the navigation pane, choose **Access Keys**.
#. Click **Create Access Key**. The **Create Access Key** dialog box is displayed.
#. Click **OK** to download the access key.

Creating a Secret Using an Access Key
-------------------------------------

#. Obtain an access key.

#. Encode the keys using Base64. (Assume that the obtained AK is **xxx** and the SK is **yyy**.)

   .. code-block::

      echo -n xxx|base64
      echo -n yyy|base64

   Record the encoded AK and SK.

#. Create a YAML file for the secret, for example, **test-user.yaml**.

   .. code-block::

      apiVersion: v1
      data:
        access.key: WE5WWVhVNU*****
        secret.key: Nnk4emJyZ0*****
      kind: Secret
      metadata:
        name: test-user
        namespace: default
        labels:
          secret.kubernetes.io/used-by: csi
      type: cfe/secure-opaque

   Specifically:

   +-----------------------------------+---------------------------------------------------------------------------------------------------+
   | Parameter                         | Description                                                                                       |
   +===================================+===================================================================================================+
   | access.key                        | A Base64-encoded AK                                                                               |
   +-----------------------------------+---------------------------------------------------------------------------------------------------+
   | secret.key                        | A Base64-encoded SK                                                                               |
   +-----------------------------------+---------------------------------------------------------------------------------------------------+
   | name                              | Secret name                                                                                       |
   +-----------------------------------+---------------------------------------------------------------------------------------------------+
   | namespace                         | Namespace of a secret                                                                             |
   +-----------------------------------+---------------------------------------------------------------------------------------------------+
   | secret.kubernetes.io/used-by: csi | Add this label if you want to make it available on the CCE console when you create an OBS PV/PVC. |
   +-----------------------------------+---------------------------------------------------------------------------------------------------+
   | type                              | Secret type. The value must be **cfe/secure-opaque**.                                             |
   |                                   |                                                                                                   |
   |                                   | When this type is used, the data entered by users is automatically encrypted.                     |
   +-----------------------------------+---------------------------------------------------------------------------------------------------+

#. Create the secret.

   .. code-block::

      kubectl create -f test-user.yaml

Specifying a Secret for Mounting During Static Creation of an OBS Volume
------------------------------------------------------------------------

After a secret is created using the AK/SK, you can associate the secret with the PV to be created and then use the AK/SK in the secret to mount an OBS volume.

#. Log in to the OBS console, create an OBS bucket, and record the bucket name and StorageClass. The parallel file system is used as an example.

#. Create a YAML file for the PV, for example, **pv-example.yaml**.

   .. code-block::

      apiVersion: v1
      kind: PersistentVolume
      metadata:
        name: pv-obs-example
        annotations:
          pv.kubernetes.io/provisioned-by: everest-csi-provisioner
      spec:
        accessModes:
        - ReadWriteMany
        capacity:
          storage: 1Gi
        csi:
          nodePublishSecretRef:
            name: test-user
            namespace: default
          driver: obs.csi.everest.io
          fsType: obsfs
          volumeAttributes:
            everest.io/obs-volume-type: STANDARD
            everest.io/region: eu-de
            storage.kubernetes.io/csiProvisionerIdentity: everest-csi-provisioner
          volumeHandle: obs-normal-static-pv
        persistentVolumeReclaimPolicy: Delete
        storageClassName: csi-obs

   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                         | Description                                                                                                                                                           |
   +===================================+=======================================================================================================================================================================+
   | nodePublishSecretRef              | Secret specified during the mounting                                                                                                                                  |
   |                                   |                                                                                                                                                                       |
   |                                   | -  **name**: name of the secret                                                                                                                                       |
   |                                   | -  **namespace**: The namespace of the secret                                                                                                                         |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | fsType                            | File type, which can be **s3fs** or **obsfs**. If the value is **s3fs**, an OBS bucket is created. If the value is **obsfs**, an OBS parallel file system is created. |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | volumeHandle                      | OBS bucket name.                                                                                                                                                      |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Create a PV.

   .. code-block::

      kubectl create -f pv-example.yaml

   After a PV is created, you can create a PVC and associate it with the PV.

#. Create a YAML file for the PVC, for example, **pvc-example.yaml**.

   **Example YAML file for the PVC:**

   .. code-block::

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        annotations:
          csi.storage.k8s.io/node-publish-secret-name: test-user
          csi.storage.k8s.io/node-publish-secret-namespace: default
          volume.beta.kubernetes.io/storage-provisioner: everest-csi-provisioner
          everest.io/obs-volume-type: STANDARD
          csi.storage.k8s.io/fstype: obsfs
        name: obs-secret
        namespace: default
      spec:
        accessModes:
        - ReadWriteMany
        resources:
          requests:
            storage: 1Gi
        storageClassName: csi-obs
        volumeName: pv-obs-example

   +--------------------------------------------------+---------------------------+
   | Parameter                                        | Description               |
   +==================================================+===========================+
   | csi.storage.k8s.io/node-publish-secret-name      | The name of a secret      |
   +--------------------------------------------------+---------------------------+
   | csi.storage.k8s.io/node-publish-secret-namespace | The namespace of a secret |
   +--------------------------------------------------+---------------------------+

#. Create a PVC.

   .. code-block::

      kubectl create -f pvc-example.yaml

   After the PVC is created, you can create a workload and associate it with the PVC to create volumes.

Specifying a Secret for Mounting During Dynamic Creation of an OBS Volume
-------------------------------------------------------------------------

When dynamically creating an OBS volume, you can use the following method to specify a secret:

#. Create a YAML file for the PVC, for example, **pvc-example.yaml**.

   .. code-block::

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        annotations:
          csi.storage.k8s.io/node-publish-secret-name: test-user
          csi.storage.k8s.io/node-publish-secret-namespace: default
          everest.io/obs-volume-type: STANDARD
          csi.storage.k8s.io/fstype: obsfs
        name: obs-secret
        namespace: default
      spec:
        accessModes:
        - ReadWriteMany
        resources:
          requests:
            storage: 1Gi
        storageClassName: csi-obs

   +--------------------------------------------------+---------------------------+
   | Parameter                                        | Description               |
   +==================================================+===========================+
   | csi.storage.k8s.io/node-publish-secret-name      | The name of a secret      |
   +--------------------------------------------------+---------------------------+
   | csi.storage.k8s.io/node-publish-secret-namespace | The namespace of a secret |
   +--------------------------------------------------+---------------------------+

#. Create a PVC.

   .. code-block::

      kubectl create -f pvc-example.yaml

   After the PVC is created, you can create a workload and associate it with the PVC to create volumes.

Verification
------------

You can use a secret of an IAM user to mount an OBS volume. Assume that a workload named **obs-secret** is created, the mount path in the container is **/temp**, and the IAM user has the CCE **ReadOnlyAccess** and **Tenant Guest** permissions.

#. Query the name of the workload pod.

   .. code-block::

      kubectl get pod | grep obs-secret

   Expected outputs:

   .. code-block::

      obs-secret-5cd558f76f-vxslv          1/1     Running   0          3m22s

#. Query the objects in the mount path. In this example, the query is successful.

   .. code-block::

      kubectl exec obs-secret-5cd558f76f-vxslv -- ls -l /temp/

#. Write data into the mount path. In this example, the write operation failed.

   .. code-block::

      kubectl exec obs-secret-5cd558f76f-vxslv -- touch /temp/test

   Expected outputs:

   .. code-block::

      touch: setting times of '/temp/test': No such file or directory
      command terminated with exit code 1

#. Set the read/write permissions for the IAM user who mounted the OBS volume by referring to the bucket policy configuration.

   |image2|

#. Write data into the mount path again. In this example, the write operation succeeded.

   .. code-block::

      kubectl exec obs-secret-5cd558f76f-vxslv -- touch /temp/test

#. Check the mount path in the container to see whether the data is successfully written.

   .. code-block::

      kubectl exec obs-secret-5cd558f76f-vxslv -- ls -l /temp/

   Expected outputs:

   .. code-block::

      -rwxrwxrwx 1 root root 0 Jun  7 01:52 test

.. |image1| image:: /_static/images/en-us_image_0000002484119690.png
.. |image2| image:: /_static/images/en-us_image_0000002516079657.png
