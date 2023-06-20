:original_name: cce_10_0263.html

.. _cce_10_0263:

Creating a Deployment Mounted with an SFS Volume
================================================

Scenario
--------

After an SFS volume is created or imported to CCE, you can mount the volume to a workload.

Prerequisites
-------------

You have created a cluster and installed the CSI plug-in (:ref:`everest <cce_10_0066>`) in the cluster.

Notes and Constraints
---------------------

The following configuration example applies to clusters of Kubernetes 1.15 or later.

Procedure
---------

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Run the following commands to configure the **sfs-deployment-example.yaml** file, which is used to create a pod.

   **touch sfs-deployment-example.yaml**

   **vi sfs-deployment-example.yaml**

   Example of mounting an SFS volume to a Deployment (PVC-based, shared volume):

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: sfs-deployment-example                                # Workload name
        namespace: default
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: sfs-deployment-example
        template:
          metadata:
            labels:
              app: sfs-deployment-example
          spec:
            containers:
            - image: nginx
              name: container-0
              volumeMounts:
              - mountPath: /tmp                                # Mount path
                name: pvc-sfs-example
            imagePullSecrets:
              - name: default-secret
            restartPolicy: Always
            volumes:
            - name: pvc-sfs-example
              persistentVolumeClaim:
                claimName: pvc-sfs-auto-example                # PVC name

   .. note::

      **spec.template.spec.containers.volumeMounts.name** and **spec.template.spec.volumes.name** must be consistent because they have a mapping relationship.

#. Run the following command to create the workload:

   **kubectl create -f sfs-deployment-example.yaml**
