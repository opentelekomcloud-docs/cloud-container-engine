:original_name: cce_10_0336.html

.. _cce_10_0336:

Using a Custom Access Key (AK/SK) to Mount an OBS Volume
========================================================

:ref:`CCE Container Storage (Everest) <cce_10_0066>` supports custom access keys. In this way, IAM users can use their own custom access keys to mount an OBS volume. You can use IAM to control the OBS access permissions of IAM users.

Prerequisites
-------------

-  The :ref:`CCE Container Storage (Everest) <cce_10_0066>` version must be 1.2.8 or later.
-  The cluster version must be 1.15.11 or later.

Constraints
-----------

-  When an OBS volume is mounted using custom access keys (AK/SK), the access key cannot be deleted or disabled. Otherwise, the service container cannot access the mounted OBS volume.
-  Custom access keys cannot be configured for secure containers.

Disabling Access Keys
---------------------

When creating an OBS volume on the console of the old edition, you need to upload the AK/SK, which are global access keys used for mounting OBS volumes. As a result, all IAM users in your account will use the same keys to mount OBS buckets as volumes, and they will have identical permissions on the buckets. However, this setting does not allow you to set different permissions for individual IAM users.

If you have uploaded the AK/SK (specifically, if **paas.longaksk** exists in the **kube-system** namespace of the cluster), you should disable the global access secret to prevent IAM users from performing unauthorized operations. This ensures that the uploaded global access secret in the console will not be used when OBS volumes are used. If you have not uploaded any AK/SK, skip this section.

.. note::

   -  Before disabling the global access secret, ensure that no OBS volume is used by the current cluster. Otherwise, this volume fails to be mounted when the workload that has the volume mounted is scaled out or restarted because the access keys must be specified.
   -  After the global access secret is disabled, you must specify the access keys when creating a PV and PVC. Otherwise, the OBS volume fails to be mounted.

To disable the global access secret, do as follows:

-  Disable the automatic mounting of access secrets in the Everest add-on by setting **disable_auto_mount_secret** to **true**.

   The following steps apply when the add-on version is 2.\ *x* (for example, 2.1.42) or later.

   #. Log in to the CCE console and click the cluster name to access the cluster console.
   #. In the navigation pane, choose **Add-ons**. In the right pane, find the CCE Container Storage (Everest) add-on and click **Edit**.
   #. Configure the add-on parameters. Set **Prohibit Global Secret from Mounting Object Storage (disable_auto_mount_secret)** to **Yes**.
   #. Click **OK**.

   The following steps apply when the add-on version is 1.\ *x*. (The modified settings cannot be retained during the add-on upgrades. You are advised to use the add-on v2.\ *x*.)

   #. Use kubectl to access the cluster and run the following command to modify the add-on configuration:

      .. code-block::

         kubectl edit ds everest-csi-driver -nkube-system

   #. Search for **disable-auto-mount-secret** and set it to **true**.

      |image1|

   #. Run **:wq** to save the modification and exit. Wait until the pod is restarted.

-  In the :ref:`Settings \> Cluster Settings <cce_10_0782__section138274223718>` area, disable the global access secret of the cluster. The global access secret (**paas.longaksk**) in the **kube-system** namespace of the cluster will be deleted.

.. _cce_10_0336__section4633162355911:

Obtaining Access Keys
---------------------

#. Access the **My Credentials** page.
#. In the navigation pane, choose **Access Keys**.
#. Click **Create Access Key**. The **Create Access Key** dialog box is displayed.
#. Click **OK** to download the access key.

Creating a Secret Using Access Keys
-----------------------------------

#. Obtain access keys.

#. Encode the access keys using Base64. (Assume that the AK is xxx and the SK is yyy.)

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
   | namespace                         | Namespace of the secret                                                                           |
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
   | nodePublishSecretRef              | Secret specified during the mounting.                                                                                                                                 |
   |                                   |                                                                                                                                                                       |
   |                                   | -  **name**: name of the secret                                                                                                                                       |
   |                                   | -  **namespace**: namespace of the secret                                                                                                                             |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | fsType                            | File type, which can be **s3fs** or **obsfs**. If the value is **s3fs**, an OBS bucket is created. If the value is **obsfs**, an OBS parallel file system is created. |
   +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | volumeHandle                      | OBS volume name.                                                                                                                                                      |
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

   ================================================ =======================
   Parameter                                        Description
   ================================================ =======================
   csi.storage.k8s.io/node-publish-secret-name      Secret name
   csi.storage.k8s.io/node-publish-secret-namespace Namespace of the secret
   ================================================ =======================

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

   ================================================ =======================
   Parameter                                        Description
   ================================================ =======================
   csi.storage.k8s.io/node-publish-secret-name      Secret name
   csi.storage.k8s.io/node-publish-secret-namespace Namespace of the secret
   ================================================ =======================

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

.. |image1| image:: /_static/images/en-us_image_0000002631690616.png
.. |image2| image:: /_static/images/en-us_image_0000002662009849.png
