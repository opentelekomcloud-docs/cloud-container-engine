:original_name: cce_10_0321.html

.. _cce_10_0321:

(kubectl) Creating a StatefulSet Mounted with an SFS Volume
===========================================================

Scenario
--------

CCE allows you to use an existing SFS volume to create a StatefulSet through a PersistentVolumeClaim (PVC).

Notes and Constraints
---------------------

The following configuration example applies to clusters of Kubernetes 1.13 or earlier.

Procedure
---------

#. Create an SFS volume by referring to :ref:`(kubectl) Automatically Creating an SFS Volume <cce_10_0318>` and record the volume name.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create a YAML file for creating the workload. Assume that the file name is **sfs-statefulset-example**.\ **yaml**.

   **touch sfs-statefulset-example.yaml**

   **vi sfs-statefulset-example.yaml**

   **Example YAML:**

   .. code-block::

      apiVersion: apps/v1
      kind: StatefulSet
      metadata:
        name: sfs-statefulset-example
        namespace: default
      spec:
        replicas: 2
        selector:
          matchLabels:
            app: sfs-statefulset-example
        serviceName: qwqq
        template:
          metadata:
            annotations:
              metrics.alpha.kubernetes.io/custom-endpoints: '[{"api":"","path":"","port":"","names":""}]'
              pod.alpha.kubernetes.io/initialized: "true"
            labels:
              app: sfs-statefulset-example
          spec:
            affinity: {}
            containers:
            - image: nginx:latest
              name: container-0
              volumeMounts:
              - mountPath: /tmp
                name: pvc-sfs-example
            imagePullSecrets:
            - name: default-secret
            volumes:
              - name: pvc-sfs-example
                persistentVolumeClaim:
                  claimName: cce-sfs-demo

   .. table:: **Table 1** Key parameters

      +--------------------------------------------------+-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | Parent Parameter                                 | Parameter   | Description                                                                                                                        |
      +==================================================+=============+====================================================================================================================================+
      | spec                                             | replicas    | Number of pods.                                                                                                                    |
      +--------------------------------------------------+-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | metadata                                         | name        | Name of the created workload.                                                                                                      |
      +--------------------------------------------------+-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | spec.template.spec.containers                    | image       | Image used by the workload.                                                                                                        |
      +--------------------------------------------------+-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | spec.template.spec.containers.volumeMounts       | mountPath   | Mount path in the container.                                                                                                       |
      +--------------------------------------------------+-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | spec                                             | serviceName | Service corresponding to the workload. For details about how to create a Service, see :ref:`Creating a StatefulSet <cce_10_0048>`. |
      +--------------------------------------------------+-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | spec.template.spec.volumes.persistentVolumeClaim | claimName   | Name of an existing PVC.                                                                                                           |
      +--------------------------------------------------+-------------+------------------------------------------------------------------------------------------------------------------------------------+

   .. note::

      **spec.template.spec.containers.volumeMounts.name** and **spec.template.spec.volumes.name** must be consistent because they have a mapping relationship.

#. Create the StatefulSet.

   **kubectl create -f sfs-statefulset-example .yaml**
