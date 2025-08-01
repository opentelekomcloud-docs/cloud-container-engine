:original_name: cce_10_0336.html

.. _cce_10_0336:

Using a Custom Access Key (AK/SK) to Mount an OBS Volume
========================================================

Scenario
--------

:ref:`CCE Container Storage (Everest) <cce_10_0066>` of v1.2.8 or later supports custom access keys. In this way, IAM users can use their own custom access keys to mount an OBS volume.

Prerequisites
-------------

-  The :ref:`CCE Container Storage (Everest) <cce_10_0066>` version must be 2.1.46 or later.
-  The cluster version must be 1.15.11 or later.

Notes and Constraints
---------------------

-  When an OBS volume is mounted using a custom access key (AK/SK), the access key cannot be deleted or disabled. Otherwise, the service container cannot access the mounted OBS volume.
-  Custom access keys cannot be configured for Kata containers.

Disabling Auto Key Mounting
---------------------------

On the earlier version's console, you need to upload the AK/SK, which is then used by default for mounting an OBS volume. As a result, all IAM users within your account will use the same key to mount OBS buckets, and they will have identical permissions on the buckets. However, this setting does not allow you to set different permissions for individual IAM users.

If you have uploaded the AK/SK, disable the automatic mounting of access keys by enabling the **DISABLE_AUTO_MOUNT_SECRET** parameter in the Everest add-on to prevent IAM users from performing unauthorized operations. In this way, the access keys uploaded on the console will not be used when you use OBS volumes.

.. note::

   -  Before enabling **DISABLE_AUTO_MOUNT_SECRET**, ensure that there are no OBS volumes in the cluster. Workloads using OBS volumes may fail to remount after scaling or restart due to missing access keys, which are blocked by **DISABLE_AUTO_MOUNT_SECRET**.
   -  If **DISABLE_AUTO_MOUNT_SECRET** is set to **true**, an access key must be specified when a PV or PVC is created. Otherwise, mounting the OBS volume will fail.

#. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Add-ons**, locate **CCE Container Storage (Everest)** on the right, and click **Edit**.
#. Configure the add-on parameters. Set **Prohibit Global Secret from Mounting Object Storage (disable_auto_mount_secret)** to **Yes**.
#. Click **OK**.

.. _cce_10_0336__section4633162355911:

Obtaining an Access Key
-----------------------

#. Log in to the console.
#. Hover the cursor over the username in the upper right corner and choose **My Credentials** from the drop-down list.
#. In the navigation pane, choose **Access Keys**.
#. Click **Create Access Key**. The **Create Access Key** dialog box is displayed.
#. Click **OK** to download the access key.

Creating a Secret Using an Access Key
-------------------------------------

#. Obtain an access key.

#. Encode the keys using Base64. (Assume that the AK is xxx and the SK is yyy.)

   **echo -n xxx|base64**

   **echo -n yyy|base64**

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

   +-----------------------------------+--------------------------------------------------------------------------------------------------------------------+
   | Parameter                         | Description                                                                                                        |
   +===================================+====================================================================================================================+
   | access.key                        | Base64-encoded AK.                                                                                                 |
   +-----------------------------------+--------------------------------------------------------------------------------------------------------------------+
   | secret.key                        | Base64-encoded SK.                                                                                                 |
   +-----------------------------------+--------------------------------------------------------------------------------------------------------------------+
   | name                              | Secret name.                                                                                                       |
   +-----------------------------------+--------------------------------------------------------------------------------------------------------------------+
   | namespace                         | Namespace of the secret.                                                                                           |
   +-----------------------------------+--------------------------------------------------------------------------------------------------------------------+
   | secret.kubernetes.io/used-by: csi | Add this label in the YAML file if you want to make it available on the CCE console when you create an OBS PV/PVC. |
   +-----------------------------------+--------------------------------------------------------------------------------------------------------------------+
   | type                              | Secret type. The value must be **cfe/secure-opaque**.                                                              |
   |                                   |                                                                                                                    |
   |                                   | When this type is used, the data entered by users is automatically encrypted.                                      |
   +-----------------------------------+--------------------------------------------------------------------------------------------------------------------+

#. Create the secret.

   **kubectl create -f test-user.yaml**

Mounting a Secret When Statically Creating an OBS Volume
--------------------------------------------------------

After a secret is created using the AK/SK, you can associate the secret with the PV to be created and then use the AK/SK in the secret to mount an OBS volume.

#. Log in to the OBS console, create an OBS bucket, and record the bucket name and storage class. The parallel file system is used as an example.

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
   | nodePublishSecretRef              | Secret specified during the mounting.                                                                                                                                 |
   |                                   |                                                                                                                                                                       |
   |                                   | -  **name**: name of the secret                                                                                                                                       |
   |                                   | -  **namespace**: namespace of the secret                                                                                                                             |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | fsType                            | File type, which can be **s3fs** or **obsfs**. If the value is **s3fs**, an OBS bucket is created. If the value is **obsfs**, an OBS parallel file system is created. |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | volumeHandle                      | OBS bucket name.                                                                                                                                                      |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Create a PV.

   **kubectl create -f pv-example.yaml**

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

   ================================================ =======================
   Parameter                                        Description
   ================================================ =======================
   csi.storage.k8s.io/node-publish-secret-name      Name of the secret
   csi.storage.k8s.io/node-publish-secret-namespace Namespace of the secret
   ================================================ =======================

#. Create a PVC.

   **kubectl create -f pvc-example.yaml**

   After the PVC is created, you can create a workload and associate it with the PVC to create volumes.

Mounting a Secret When Dynamically Creating an OBS Volume
---------------------------------------------------------

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

   ================================================ =======================
   Parameter                                        Description
   ================================================ =======================
   csi.storage.k8s.io/node-publish-secret-name      Name of the secret
   csi.storage.k8s.io/node-publish-secret-namespace Namespace of the secret
   ================================================ =======================

#. Create a PVC.

   **kubectl create -f pvc-example.yaml**

   After the PVC is created, you can create a workload and associate it with the PVC to create volumes.

Verification
------------

You can use a secret of an IAM user to mount an OBS volume. Assume that a workload named **obs-secret** is created, the mount path in the container is **/temp**, and the IAM user has the CCE **ReadOnlyAccess** and **Tenant Guest** permissions.

#. Query the name of the workload pod.

   **kubectl get po \| grep obs-secret**

   Expected outputs:

   .. code-block::

      obs-secret-5cd558f76f-vxslv          1/1     Running   0          3m22s

#. Query the objects in the mount path. In this example, the query is successful.

   **kubectl exec obs-secret-5cd558f76f-vxslv -- ls -l /temp/**

#. Write data into the mount path. In this example, the write operation failed.

   **kubectl exec obs-secret-5cd558f76f-vxslv -- touch /temp/test**

   Expected outputs:

   .. code-block::

      touch: setting times of '/temp/test': No such file or directory
      command terminated with exit code 1

#. Set the read/write permissions for the IAM user who mounted the OBS volume by referring to the bucket policy configuration.

   |image1|

#. Write data into the mount path again. In this example, the write operation succeeded.

   **kubectl exec obs-secret-5cd558f76f-vxslv -- touch /temp/test**

#. Check the mount path in the container to see whether the data is successfully written.

   **kubectl exec obs-secret-5cd558f76f-vxslv -- ls -l /temp/**

   Expected outputs:

   .. code-block::

      -rwxrwxrwx 1 root root 0 Jun  7 01:52 test

.. |image1| image:: /_static/images/en-us_image_0000002253780093.png
