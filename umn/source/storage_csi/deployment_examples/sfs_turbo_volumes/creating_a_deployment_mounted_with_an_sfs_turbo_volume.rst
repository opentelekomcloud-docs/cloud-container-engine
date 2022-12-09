:original_name: cce_01_0274.html

.. _cce_01_0274:

Creating a Deployment Mounted with an SFS Turbo Volume
======================================================

Scenario
--------

After an SFS Turbo volume is created or imported to CCE, you can mount the volume to a workload.

Prerequisites
-------------

You have created a CCE cluster and installed the CSI plug-in (:ref:`everest <cce_01_0066>`) in the cluster.

Notes and Constraints
---------------------

The following configuration example applies to clusters of Kubernetes 1.15 or later.

Procedure
---------

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_01_0107>`.

#. Run the following commands to configure the **sfsturbo-deployment-example.yaml** file, which is used to create a Deployment:

   **touch sfsturbo-deployment-example.yaml**

   **vi sfsturbo-deployment-example.yaml**

   Example of mounting an SFS Turbo volume to a Deployment (PVC-based, shared volume):

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: sfsturbo-deployment-example                                # Workload name
        namespace: default
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: sfsturbo-deployment-example
        template:
          metadata:
            labels:
              app: sfsturbo-deployment-example
          spec:
            containers:
            - image: nginx
              name: container-0
              volumeMounts:
              - mountPath: /tmp                                # Mount path
                name: pvc-sfsturbo-example
            restartPolicy: Always
            imagePullSecrets:
              - name: default-secret
            volumes:
            - name: pvc-sfsturbo-example
              persistentVolumeClaim:
                claimName: pvc-sfsturbo-example                # PVC name

   .. table:: **Table 1** Key parameters

      +-----------+---------------------------------------------------------------------------+
      | Parameter | Description                                                               |
      +===========+===========================================================================+
      | name      | Name of the created Deployment.                                           |
      +-----------+---------------------------------------------------------------------------+
      | app       | Name of the Deployment.                                                   |
      +-----------+---------------------------------------------------------------------------+
      | mountPath | Mount path of the container. In this example, the mount path is **/tmp**. |
      +-----------+---------------------------------------------------------------------------+

   .. note::

      **spec.template.spec.containers.volumeMounts.name** and **spec.template.spec.volumes.name** must be consistent because they have a mapping relationship.

#. Run the following command to create the workload:

   **kubectl create -f sfsturbo-deployment-example.yaml**
