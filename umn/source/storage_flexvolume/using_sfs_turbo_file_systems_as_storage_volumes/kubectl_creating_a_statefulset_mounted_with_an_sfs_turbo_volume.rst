:original_name: cce_10_0334.html

.. _cce_10_0334:

(kubectl) Creating a StatefulSet Mounted with an SFS Turbo Volume
=================================================================

Scenario
--------

CCE allows you to use an existing SFS Turbo volume to create a StatefulSet.

Notes and Constraints
---------------------

The following configuration example applies to clusters of Kubernetes 1.13 or earlier.

Procedure
---------

#. Create an SFS Turbo volume and record the volume name.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create a YAML file for creating the workload. Assume that the file name is **efs-statefulset-example.yaml**.

   **touch efs-statefulset-example.yaml**

   **vi efs-statefulset-example.yaml**

   **Example YAML:**

   .. code-block::

      apiVersion: apps/v1
      kind: StatefulSet
      metadata:
        name: efs-statefulset-example
        namespace: default
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: efs-statefulset-example
        template:
          metadata:
            annotations:
              metrics.alpha.kubernetes.io/custom-endpoints: '[{"api":"","path":"","port":"","names":""}]'
              pod.alpha.kubernetes.io/initialized: 'true'
            labels:
              app: efs-statefulset-example
          spec:
            containers:
              - image: 'nginx:1.0.0'
                name: container-0
                resources:
                  requests: {}
                  limits: {}
                env:
                  - name: PAAS_APP_NAME
                    value: efs-statefulset-example
                  - name: PAAS_NAMESPACE
                    value: default
                  - name: PAAS_PROJECT_ID
                    value: b18296881cc34f929baa8b9e95abf88b
                volumeMounts:
                  - name: efs-statefulset-example
                    mountPath: /tmp
                    readOnly: false
                    subPath: ''
            imagePullSecrets:
              - name: default-secret
            terminationGracePeriodSeconds: 30
            volumes:
              - persistentVolumeClaim:
                  claimName: cce-efs-import-jnr481gm-3y5o
                name: efs-statefulset-example
            affinity: {}
            tolerations:
              - key: node.kubernetes.io/not-ready
                operator: Exists
                effect: NoExecute
                tolerationSeconds: 300
              - key: node.kubernetes.io/unreachable
                operator: Exists
                effect: NoExecute
                tolerationSeconds: 300
        podManagementPolicy: OrderedReady
        serviceName: test
        updateStrategy:
          type: RollingUpdate

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

   .. note::

      **spec.template.spec.containers.volumeMounts.name** and **spec.template.spec.volumes.name** must be consistent because they have a mapping relationship.

#. Create the StatefulSet.

   **kubectl create -f efs-statefulset-example.yaml**
