:original_name: cce_bestpractice_0314.html

.. _cce_bestpractice_0314:

Troubleshooting
===============

.. _cce_bestpractice_0314__section11197194820367:

hostPath Volumes Cannot Be Backed Up
------------------------------------

Both hostPath and local volumes are local storage volumes. However, restic, which is integrated into Velero, cannot back up hostPath volumes. It can only back up local volumes. So, you need to change the hostPath volumes to local ones in the source cluster.

.. note::

   It is recommended that local volumes be used in Kubernetes v1.10 or later. Such volumes can only be statically created. For details, see `local <https://kubernetes.io/docs/concepts/storage/volumes/#local>`__.

#. Create a storage class for a local volume.

   Example YAML:

   .. code-block::

      apiVersion: storage.k8s.io/v1
      kind: StorageClass
      metadata:
        name: local
      provisioner: kubernetes.io/no-provisioner
      volumeBindingMode: WaitForFirstConsumer

#. Change the **hostPath** field to the **local** field, specify the original local disk path of the node, and add the **nodeAffinity** field.

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

#. Verify the creation result.

   .. code-block::

      kubectl get pv

   Information similar to the following is displayed:

   .. code-block::

      NAME       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM               STORAGECLASS   REASON   AGE
      mysql-pv   5Gi        RWO            Delete           Available                       local                   3s

.. _cce_bestpractice_0314__section321054511332:

Backup Tool Resources Are Insufficient
--------------------------------------

In a production environment with numerous resources to be backed up, using the backup tool with the default resource size may lead to insufficient resources. In such case, take the following steps to adjust the vCPU and memory settings of Velero and restic:

**Before installing Velero:**

You can specify the size of resources used by Velero and restic when :ref:`installing Velero <cce_bestpractice_0310__li1722825643415>`.

The following is an example of installation parameters:

.. code-block::

   velero install \
      --velero-pod-cpu-request 500m \
      --velero-pod-mem-request 1Gi \
      --velero-pod-cpu-limit 1000m \
      --velero-pod-mem-limit 1Gi \
      --use-node-agent \
      --node-agent-pod-cpu-request 500m \
      --node-agent-pod-mem-request 1Gi \
      --node-agent-pod-cpu-limit 1000m \
      --node-agent-pod-mem-limit 1Gi

**After Velero is installed:**

#. Edit the YAML files of the Velero and node-agent workloads in the **velero** namespace.

   .. code-block::

      kubectl edit deploy velero -n velero
      kubectl edit ds node-agent -n velero

#. Modify the resource size under the **resources** field. The modification is the same for the Velero and restic workloads, as shown in the following:

   .. code-block::

      resources:
        limits:
          cpu: "1"
          memory: 1Gi
        requests:
          cpu: 500m
          memory: 1Gi
