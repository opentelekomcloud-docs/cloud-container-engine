:original_name: cce_10_0262.html

.. _cce_10_0262:

Creating a StatefulSet Mounted with an SFS Volume
=================================================

Scenario
--------

CCE allows you to use an existing SGS volume to create a StatefulSet (by using a PVC).

Prerequisites
-------------

You have created a cluster and installed the CSI plug-in (:ref:`everest <cce_10_0066>`) in the cluster.

Notes and Constraints
---------------------

The following configuration example applies to clusters of Kubernetes 1.15 or later.

Procedure
---------

#. Create an SFS volume by referring to :ref:`PersistentVolumeClaims (PVCs) <cce_10_0378>` and record the volume name.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create a YAML file for creating the workload. Assume that the file name is **sfs-statefulset-example**.\ **yaml**.

   **touch sfs-statefulset-example.yaml**

   **vi sfs-statefulset-example.yaml**

   Configuration example:

   .. code-block::

      apiVersion: apps/v1
      kind: StatefulSet
      metadata:
        name: sfs-statefulset-example
        namespace: default
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: sfs-statefulset-example
        template:
          metadata:
            labels:
              app: sfs-statefulset-example
          spec:
            volumes:
            - name: pvc-sfs-example
              persistentVolumeClaim:
                claimName: pvc-sfs-example
            containers:
            - name: container-0
              image: 'nginx:latest'
              volumeMounts:
                - name: pvc-sfs-example
                  mountPath: /tmp
            restartPolicy: Always
            imagePullSecrets:
            - name: default-secret
        serviceName: sfs-statefulset-example-headless
        updateStrategy:
          type: RollingUpdate

   .. table:: **Table 1** Key parameters

      +--------------------------------------------------+-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | Parent Parameter                                 | Parameter   | Description                                                                                                                        |
      +==================================================+=============+====================================================================================================================================+
      | spec                                             | replicas    | Number of pods.                                                                                                                    |
      +--------------------------------------------------+-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | metadata                                         | name        | Name of the new workload.                                                                                                          |
      +--------------------------------------------------+-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | spec.template.spec.containers                    | image       | Image used by the workload.                                                                                                        |
      +--------------------------------------------------+-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | spec.template.spec.containers.volumeMounts       | mountPath   | Mount path of a container.                                                                                                         |
      +--------------------------------------------------+-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | spec                                             | serviceName | Service corresponding to the workload. For details about how to create a Service, see :ref:`Creating a StatefulSet <cce_10_0048>`. |
      +--------------------------------------------------+-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | spec.template.spec.volumes.persistentVolumeClaim | claimName   | Name of an existing PVC.                                                                                                           |
      +--------------------------------------------------+-------------+------------------------------------------------------------------------------------------------------------------------------------+

   Example of mounting an SFS volume to a StatefulSet (PVC template-based, dedicated volume):

   **Example YAML file:**

   .. code-block::

      apiVersion: apps/v1
      kind: StatefulSet
      metadata:
        name: sfs-statefulset-example
        namespace: default
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: sfs-statefulset-example
        template:
          metadata:
            labels:
              app: sfs-statefulset-example
          spec:
            containers:
              - name: container-0
                image: 'nginx:latest'
                volumeMounts:
                  - name: pvc-sfs-auto-example
                    mountPath: /tmp
            restartPolicy: Always
            imagePullSecrets:
              - name: default-secret
        volumeClaimTemplates:
          - metadata:
              name: pvc-sfs-auto-example
              namespace: default
            spec:
              accessModes:
                - ReadWriteMany
              resources:
                requests:
                  storage: 10Gi
              storageClassName: csi-nas
        serviceName: sfs-statefulset-example-headless
        updateStrategy:
          type: RollingUpdate

   .. note::

      **spec.template.spec.containers.volumeMounts.name** and **spec.template.spec.volumes.name** must be consistent because they have a mapping relationship.

#. Create a StatefulSet.

   **kubectl create -f sfs-statefulset-example.yaml**
