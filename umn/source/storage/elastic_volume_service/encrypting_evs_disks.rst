:original_name: cce_10_0859.html

.. _cce_10_0859:

Encrypting EVS Disks
====================

Encrypting cloud disks ensures data privacy and control, making it ideal for scenarios that demand high security or compliance standards. This section describes how to use the keys managed by Data Encryption Workshop (DEW) to encrypt EVS disks.

Prerequisites
-------------

-  You have created a cluster and installed the :ref:`CCE Container Storage (Everest) <cce_10_0066>` add-on in the cluster.
-  An available key has been created in DEW.
-  To create a cluster using commands, ensure kubectl is used. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

Using the Console
-----------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Dynamically create a PVC and PV.

   a. Choose **Storage** in the navigation pane and click the **PersistentVolumeClaims (PVCs)** tab. Click **Create PVC** in the upper right corner. In the dialog box displayed, configure PVC parameters.
   b. Select EVS for the storage type, enable encryption, and choose a key. Configure other parameters based on service requirements. For details, see :ref:`Using an EVS Disk Through a Dynamic PV <cce_10_0615>`.
   c. Click **Create**.

#. Go to the **PersistentVolumeClaims (PVCs)** tab and check whether the PVC of the encrypted EVS disk is created and whether the disk is encrypted.
#. The method of using an encrypted PVC is the same as that of using a regular PVC.

Automatically Creating an Encrypted EVS Disk Using kubectl
----------------------------------------------------------

#. Use kubectl to access the cluster.

#. Create the **pvc-evs-auto.yaml** file. For details, see :ref:`Automatically Creating an EVS Volume Through kubectl <cce_10_0615__section189114109321>`.

   .. code-block::

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        name: pvc-evs-auto
        namespace: default
        annotations:
          everest.io/disk-volume-type: SAS    # EVS disk
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
        storageClassName: csi-disk    # The storage class is EVS.

#. Run the following command to create a PVC:

   .. code-block::

      kubectl apply -f pvc-evs-auto.yaml

#. Go to the **PersistentVolumeClaims (PVCs)** tab and check whether the PVC of the encrypted EVS disk is created and whether the disk is encrypted.
