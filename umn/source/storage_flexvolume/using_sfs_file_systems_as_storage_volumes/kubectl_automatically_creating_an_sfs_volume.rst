:original_name: cce_10_0318.html

.. _cce_10_0318:

(kubectl) Automatically Creating an SFS Volume
==============================================

Notes and Constraints
---------------------

The following configuration example applies to clusters of Kubernetes 1.13 or earlier.

Procedure
---------

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Run the following commands to configure the **pvc-sfs-auto-example.yaml** file, which is used to create a PVC.

   **touch pvc-sfs-auto-example.yaml**

   **vi pvc-sfs-auto-example.yaml**

   **Example YAML file:**

   .. code-block::

      apiVersion: v1
      kind: PersistentVolumeClaim
      metadata:
        annotations:
          volume.beta.kubernetes.io/storage-class: nfs-rw
        name: pvc-sfs-auto-example
        namespace: default
      spec:
        accessModes:
        - ReadWriteMany
        resources:
          requests:
            storage: 10Gi

   .. table:: **Table 1** Key parameters

      +-----------------------------------------+---------------------------------------------------------------------------------------+
      | Parameter                               | Description                                                                           |
      +=========================================+=======================================================================================+
      | volume.beta.kubernetes.io/storage-class | File storage class. Currently, the standard file protocol type (nfs-rw) is supported. |
      +-----------------------------------------+---------------------------------------------------------------------------------------+
      | name                                    | Name of the PVC to be created.                                                        |
      +-----------------------------------------+---------------------------------------------------------------------------------------+
      | accessModes                             | Only **ReadWriteMany** is supported. **ReadWriteOnly** is not supported.              |
      +-----------------------------------------+---------------------------------------------------------------------------------------+
      | storage                                 | Storage capacity in the unit of Gi.                                                   |
      +-----------------------------------------+---------------------------------------------------------------------------------------+

#. Run the following command to create a PVC:

   **kubectl create -f pvc-sfs-auto-example.yaml**

   After the command is executed, a file system is created in the VPC to which the cluster belongs. Choose **Storage** > **SFS** on the CCE console or log in to the SFS console to view the file system.
