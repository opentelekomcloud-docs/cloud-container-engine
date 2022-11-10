:original_name: cce_01_0325.html

.. _cce_01_0325:

(kubectl) Automatically Creating an OBS Volume
==============================================

Scenario
--------

During the use of OBS, expected OBS buckets can be automatically created and mounted as volumes. Currently, standard and infrequent access OBS buckets are supported, which correspond to **obs-standard** and **obs-standard-ia**, respectively.

Prerequisites
-------------

-  You have created a CCE cluster and installed the FlexVolume plug-in (:ref:`storage-driver <cce_01_0127>`) in the cluster.
-  The AK/SK has been uploaded. For details, see :ref:`Preparations <cce_01_0324__section14271608324>`.

Notes and Constraints
---------------------

The following configuration example applies to clusters of Kubernetes 1.13 or earlier.

Procedure
---------

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_01_0107>`.

#. Run the following commands to configure the **pvc-obs-auto-example.yaml** file, which is used to create a PVC.

   **touch pvc-obs-auto-example.yaml**

   **vi pvc-obs-auto-example.yaml**

   **Example YAML:**

   .. code-block::

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        annotations:
          volume.beta.kubernetes.io/storage-class: obs-standard  # OBS bucket type. The value can be obs-standard (standard) or obs-standard-ia (infrequent access).
        name: pvc-obs-auto-example  # PVC name
        namespace: default
      spec:
        accessModes:
        - ReadWriteMany
        resources:
          requests:
            storage: 1Gi   # Storage capacity in the unit of Gi. For OBS buckets, this parameter is used only for verification (fixed to 1, cannot be empty or 0). Any value you set does not take effect for OBS buckets.

   .. table:: **Table 1** Key parameters

      +-----------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                               | Description                                                                                                                                                                                                    |
      +=========================================+================================================================================================================================================================================================================+
      | volume.beta.kubernetes.io/storage-class | Bucket type. Currently, **obs-standard** and **obs-standard-ia** are supported.                                                                                                                                |
      +-----------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | name                                    | Name of the PVC to be created.                                                                                                                                                                                 |
      +-----------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | accessModes                             | Only **ReadWriteMany** is supported. **ReadWriteOnly** is not supported.                                                                                                                                       |
      +-----------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | storage                                 | Storage capacity in the unit of Gi. For OBS buckets, this field is used only for verification (cannot be empty or 0). Its value is fixed at **1**, and any value you set does not take effect for OBS buckets. |
      +-----------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Run the following command to create the PVC.

   **kubectl create -f pvc-obs-auto-example.yaml**

   After the command is executed, an OBS bucket is created in the VPC to which the cluster belongs. You can click the bucket name in **Storage** > **OBS** to view the bucket or view it on the OBS console.
