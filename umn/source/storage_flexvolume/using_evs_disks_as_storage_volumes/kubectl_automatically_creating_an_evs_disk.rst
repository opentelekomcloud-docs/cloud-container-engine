:original_name: cce_01_0312.html

.. _cce_01_0312:

(kubectl) Automatically Creating an EVS Disk
============================================

Scenario
--------

CCE supports creating EVS volumes through PersistentVolumeClaims (PVCs).

Prerequisites
-------------

You have created a CCE cluster and installed the FlexVolume plug-in (:ref:`storage-driver <cce_01_0127>`) in the cluster.

Notes and Constraints
---------------------

The following configuration example applies to clusters of Kubernetes 1.13 or earlier.

Procedure
---------

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_01_0107>`.

#. Run the following commands to configure the **pvc-evs-auto-example.yaml** file, which is used to create a PVC.

   **touch pvc-evs-auto-example.yaml**

   **vi pvc-evs-auto-example.yaml**

   **Example YAML file for clusters of v1.9, v1.11, and v1.13:**

   .. code-block::

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        name: pvc-evs-auto-example
        namespace: default
        annotations:
          volume.beta.kubernetes.io/storage-class: sas
        labels:
          failure-domain.beta.kubernetes.io/region: eu-de
          failure-domain.beta.kubernetes.io/zone: eu-de-01
      spec:
        accessModes:
        - ReadWriteOnce
        resources:
          requests:
            storage: 10Gi

   .. table:: **Table 1** Key parameters

      +------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                | Description                                                                                                                          |
      +==========================================+======================================================================================================================================+
      | volume.beta.kubernetes.io/storage-class  | EVS disk type. The value is in lowercase.                                                                                            |
      |                                          |                                                                                                                                      |
      |                                          | Supported values: Common I/O (SATA), High I/O (SAS), and Ultra-high I/O (SSD)High I/O (SAS) and Ultra-high I/O (SSD)                 |
      +------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
      | failure-domain.beta.kubernetes.io/region | Region where the cluster is located.                                                                                                 |
      |                                          |                                                                                                                                      |
      |                                          | For details about the value of **region**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__. |
      +------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
      | failure-domain.beta.kubernetes.io/zone   | AZ where the EVS volume is created. It must be the same as the AZ planned for the workload.                                          |
      |                                          |                                                                                                                                      |
      |                                          | For details about the value of **zone**, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.   |
      +------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
      | storage                                  | Storage capacity in the unit of Gi.                                                                                                  |
      +------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+
      | accessModes                              | Read/write mode of the volume.                                                                                                       |
      |                                          |                                                                                                                                      |
      |                                          | You can set this parameter to **ReadWriteMany** (shared volume) and **ReadWriteOnce** (non-shared volume).                           |
      +------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------+

#. Run the following command to create a PVC.

   **kubectl create -f pvc-evs-auto-example.yaml**

   After the command is executed, an EVS disk is created in the partition where the cluster is located. Choose **Storage** > **EVS** to view the EVS disk. Alternatively, you can view the EVS disk based on the volume name on the EVS console.
