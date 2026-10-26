:original_name: cce_10_0859.html

.. _cce_10_0859:

Encrypting EVS Disks
====================

Encrypting cloud disks ensures data privacy and control, making it ideal for scenarios that demand high security or compliance standards. This section describes how to use the keys managed by the Data Encryption Workshop (DEW) service to encrypt data on cloud disks. DEW builds cryptographic service resource pools based on Cloud-hosted Hardware Security Modules (Cloud HSMs) certified by the State Cryptography Administration (SCA) to centrally manage, control, and schedule cryptographic resources on the cloud. It provides users with Virtual Security Modules (VSMs) on demand and interconnects with service applications to implement security functions such as data encryption and decryption, signature verification, key creation, and secure key storage.

How It Works
------------

The encryption system uses a two-layer key structure. The first-layer key is the customer master key (CMK), and the second-layer key is the data key (DK). The CMK encrypts and decrypts the DK to ensure their security in transit and at rest. The DK encrypts and decrypts service data. The process is as follows:

#. **Encrypt the DK**

   Before encrypting service data, the DK is encrypted by a CMK. Only encrypted DKs are stored or transferred. If an attacker gains access to an encrypted DK and service data, they cannot decrypt the data without the CMK.

#. **Encrypt data in transit and at rest**

   To read encrypted data, a decryption request is sent to KMS. KMS validates the request, decrypts the DK using the CMK, and returns the plaintext DK. The decryption is done in memory, so the plaintext DK will not be persisted to any storage medium. The system then uses the in-memory plaintext DK to decrypt disk I/O data, ensuring security both in transit and at rest.

Prerequisites
-------------

-  You have created a cluster and installed the :ref:`CCE Container Storage (Everest) <cce_10_0066>` add-on in the cluster.
-  An available key has been created in DEW.
-  To create a cluster using commands, ensure kubectl is used. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

Using the Console
-----------------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. Dynamically create a PVC and PV.

   a. Choose **Storage** in the navigation pane. In the right pane, click the **PVCs** tab. Click **Create PVC** in the upper right corner. On the slide-out panel, configure PVC parameters.
   b. Select EVS for the StorageClass, enable encryption, and choose a key. Configure other parameters based on service requirements. For details, see :ref:`Using an EVS Disk Through a Dynamic PV <cce_10_0615>`.
   c. Click **Create**.

#. Go to the **PVCs** tab and check whether the PVC of the encrypted EVS volume is created and the volume is encrypted.

   The method of using an encrypted PVC is the same as that of using a regular PVC.

Using kubectl
-------------

#. Use kubectl to access the cluster.

#. Create the **pvc-evs-auto.yaml** file. For details, see :ref:`Dynamically Creating an EVS Disk Using kubectl <cce_10_0615__section189114109321>`.

   .. code-block::

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        name: pvc-evs-auto
        namespace: default
        annotations:
          everest.io/disk-volume-type: SAS    # EVS disk type
          everest.io/crypt-key-id: 37f202db-a970-4ac1-a506-e5c4f2d7ce69   # Encryption key ID, which can be obtained from DEW
        labels:
          failure-domain.beta.kubernetes.io/region: <your_region>   # Region of the node where the application is to be deployed
          failure-domain.beta.kubernetes.io/zone: <your_zone>       # AZ of the node where the application is to be deployed
      spec:
        accessModes:
        - ReadWriteOnce               # The value must be ReadWriteOnce for EVS disks.
        resources:
          requests:
            storage: 10Gi             # EVS disk capacity, ranging from 1 to 32768
        storageClassName: csi-disk    # The StorageClass is EVS.

   .. table:: **Table 1** Encryption parameters

      +-------------------------+--------------------------------------+----------------------------------------------------------+
      | Parameter               | Example Value                        | Description                                              |
      +=========================+======================================+==========================================================+
      | everest.io/crypt-key-id | 37f202db-a970-4ac1-a506-e5c4f2d7ce69 | ID of an encryption key, which can be obtained from DEW. |
      +-------------------------+--------------------------------------+----------------------------------------------------------+

#. Run the following command to create a PVC:

   .. code-block::

      kubectl apply -f pvc-evs-auto.yaml

#. Go to the **PVCs** tab and check whether the PVC of the encrypted EVS volume is created and the volume is encrypted.
