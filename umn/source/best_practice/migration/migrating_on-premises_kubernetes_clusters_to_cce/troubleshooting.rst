:original_name: cce_bestpractice_0314.html

.. _cce_bestpractice_0314:

Troubleshooting
===============

.. _cce_bestpractice_0314__section11197194820367:

Storage Volumes of the HostPath Type Cannot Be Backed Up
--------------------------------------------------------

Both HostPath and Local volumes are local storage volumes. However, the Restic tool integrated in Velero cannot back up the PVs of the HostPath type and supports only the Local type. Therefore, you need to replace the storage volumes of the HostPath type with the Local type in the source cluster.

.. note::

   It is recommended that Local volumes be used in Kubernetes v1.10 or later and can only be statically created. For details, see `local <https://kubernetes.io/docs/concepts/storage/volumes/#local>`__.

#. Create a storage class for the Local volume.

   Example YAML:

   .. code-block::

      apiVersion: storage.k8s.io/v1
      kind: StorageClass
      metadata:
        name: local
      provisioner: kubernetes.io/no-provisioner
      volumeBindingMode: WaitForFirstConsumer

#. Change the **hostPath** field to the **local** field, specify the original local disk path of the host machine, and add the **nodeAffinity** field.

   Example YAML:

   .. code-block::

      apiVersion: v1
      kind: PersistentVolume
      metadata:
        name: mysql-pv
        labels:
          app: mysql
      spec:
        accessModes:
        - ReadWriteOnce
        capacity:
          storage: 5Gi
        storageClassName: local     # Storage class created in the previous step
        persistentVolumeReclaimPolicy: Delete
        local:
          path: "/mnt/data"     # Path of the attached local disk
        nodeAffinity:
          required:
            nodeSelectorTerms:
            - matchExpressions:
              - key: kubernetes.io/hostname
                operator: Exists

#. Run the following commands to verify the creation result:

   .. code-block::

      kubectl get pv

   Information similar to the following is displayed:

   .. code-block::

      NAME       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM               STORAGECLASS   REASON   AGE
      mysql-pv   5Gi        RWO            Delete           Available                       local                   3s

.. _cce_bestpractice_0314__section321054511332:

Backup Tool Resources Are Insufficient
--------------------------------------

In the production environment, if there are many backup resources, for example, the default resource size of the backup tool is used, the resources may be insufficient. In this case, perform the following steps to adjust the CPU and memory size allocated to the Velero and Restic:

**Before installing Velero:**

You can specify the size of resources used by Velero and Restic when :ref:`installing Velero <cce_bestpractice_0310__li1722825643415>`.

The following is an example of installation parameters:

.. code-block::

   velero install \
      --velero-pod-cpu-request 500m \
      --velero-pod-mem-request 1Gi \
      --velero-pod-cpu-limit 1000m \
      --velero-pod-mem-limit 1Gi \
      --use-restic \
      --restic-pod-cpu-request 500m \
      --restic-pod-mem-request 1Gi \
      --restic-pod-cpu-limit 1000m \
      --restic-pod-mem-limit 1Gi

**After Velero is installed:**

#. Edit the YAML files of the Velero and Restic workloads in the **velero** namespace.

   .. code-block::

      kubectl edit deploy velero -n velero
      kubectl edit deploy restic -n velero

#. Modify the resource size under the **resources** field. The modification is the same for the Velero and Restic workloads, as shown in the following:

   .. code-block::

      resources:
        limits:
          cpu: "1"
          memory: 1Gi
        requests:
          cpu: 500m
          memory: 1Gi
