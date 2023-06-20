:original_name: cce_10_0328.html

.. _cce_10_0328:

(kubectl) Creating a StatefulSet Mounted with an OBS Volume
===========================================================

Scenario
--------

CCE allows you to use an existing OBS volume to create a StatefulSet through a PersistentVolumeClaim (PVC).

Notes and Constraints
---------------------

The following configuration example applies to clusters of Kubernetes 1.13 or earlier.

Procedure
---------

#. Create an OBS volume by referring to :ref:`(kubectl) Automatically Creating an OBS Volume <cce_10_0325>` and obtain the PVC name.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create a YAML file for creating the workload. Assume that the file name is **obs-statefulset-example.yaml**.

   **touch obs-statefulset-example.yaml**

   **vi obs-statefulset-example.yaml**

   **Example YAML:**

   .. code-block::

      apiVersion: apps/v1
      kind: StatefulSet
      metadata:
        name: obs-statefulset-example
        namespace: default
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: obs-statefulset-example
        serviceName: qwqq
        template:
          metadata:
            annotations:
              metrics.alpha.kubernetes.io/custom-endpoints: '[{"api":"","path":"","port":"","names":""}]'
              pod.alpha.kubernetes.io/initialized: "true"
            creationTimestamp: null
            labels:
              app: obs-statefulset-example
          spec:
            affinity: {}
            containers:
              image: nginx:latest
              imagePullPolicy: Always
              name: container-0
              volumeMounts:
              - mountPath: /tmp
                name: pvc-obs-example
            imagePullSecrets:
            - name: default-secret
            volumes:
              - name: pvc-obs-example
                persistentVolumeClaim:
                  claimName: cce-obs-demo

   .. table:: **Table 1** Key parameters

      +-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter   | Description                                                                                                                        |
      +=============+====================================================================================================================================+
      | replicas    | Number of pods.                                                                                                                    |
      +-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | name        | Name of the created workload.                                                                                                      |
      +-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | image       | Image used by the workload.                                                                                                        |
      +-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | mountPath   | Mount path in the container.                                                                                                       |
      +-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | serviceName | Service corresponding to the workload. For details about how to create a Service, see :ref:`Creating a StatefulSet <cce_10_0048>`. |
      +-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | claimName   | Name of an existing PVC.                                                                                                           |
      +-------------+------------------------------------------------------------------------------------------------------------------------------------+

#. Create the StatefulSet.

   **kubectl create -f obs-statefulset-example.yaml**
