:original_name: cce_10_0333.html

.. _cce_10_0333:

(kubectl) Creating a Deployment Mounted with an SFS Turbo Volume
================================================================

Scenario
--------

After an SFS Turbo volume is created or imported to CCE, you can mount the volume to a workload.

Notes and Constraints
---------------------

The following configuration example applies to clusters of Kubernetes 1.13 or earlier.

Procedure
---------

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Run the following commands to configure the **efs-deployment-example.yaml** file, which is used to create a Deployment:

   **touch efs-deployment-example.yaml**

   **vi efs-deployment-example.yaml**

   Example of mounting an SFS Turbo volume to a Deployment (PVC-based, shared volume):

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: efs-deployment-example                                # Workload name
        namespace: default
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: efs-deployment-example
        template:
          metadata:
            labels:
              app: efs-deployment-example
          spec:
            containers:
            - image: nginx
              name: container-0
              volumeMounts:
              - mountPath: /tmp                                # Mount path
                name: pvc-efs-example
            restartPolicy: Always
            imagePullSecrets:
              - name: default-secret
            volumes:
            - name: pvc-efs-example
              persistentVolumeClaim:
                claimName: pvc-sfs-auto-example                # PVC name

   .. table:: **Table 1** Key parameters

      +-----------+---------------------------------------------------------------------------+
      | Parameter | Description                                                               |
      +===========+===========================================================================+
      | name      | Name of the created Deployment.                                           |
      +-----------+---------------------------------------------------------------------------+
      | app       | Name of the application running in the Deployment.                        |
      +-----------+---------------------------------------------------------------------------+
      | mountPath | Mount path in the container. In this example, the mount path is **/tmp**. |
      +-----------+---------------------------------------------------------------------------+

   .. note::

      **spec.template.spec.containers.volumeMounts.name** and **spec.template.spec.volumes.name** must be consistent because they have a mapping relationship.

#. Run the following command to create the pod:

   **kubectl create -f efs-deployment-example.yaml**

   After the creation is complete, choose **Storage** > **SFS Turbo** on the CCE console and click the PVC name. On the PVC details page, you can view the binding relationship between SFS Turbo and PVC.
