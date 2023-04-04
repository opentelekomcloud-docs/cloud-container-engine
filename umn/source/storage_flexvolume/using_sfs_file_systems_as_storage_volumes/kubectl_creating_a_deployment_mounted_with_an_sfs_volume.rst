:original_name: cce_10_0320.html

.. _cce_10_0320:

(kubectl) Creating a Deployment Mounted with an SFS Volume
==========================================================

Scenario
--------

After an SFS volume is created or imported to CCE, you can mount the volume to a workload.

Notes and Constraints
---------------------

The following configuration example applies to clusters of Kubernetes 1.13 or earlier.

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

   .. table:: **Table 1** Key parameters

      +--------------------------------------------------+-----------+---------------------------------------------------------------------------+
      | Parent Parameter                                 | Parameter | Description                                                               |
      +==================================================+===========+===========================================================================+
      | metadata                                         | name      | Name of the pod to be created.                                            |
      +--------------------------------------------------+-----------+---------------------------------------------------------------------------+
      | spec.template.spec.containers.volumeMounts       | mountPath | Mount path in the container. In this example, the mount path is **/tmp**. |
      +--------------------------------------------------+-----------+---------------------------------------------------------------------------+
      | spec.template.spec.volumes.persistentVolumeClaim | claimName | Name of an existing PVC.                                                  |
      +--------------------------------------------------+-----------+---------------------------------------------------------------------------+

   .. note::

      **spec.template.spec.containers.volumeMounts.name** and **spec.template.spec.volumes.name** must be consistent because they have a mapping relationship.

   Example of mounting an SFS volume to a StatefulSet (PVC template-based, dedicated volume):

   **Example YAML:**

   .. code-block::

      apiVersion: apps/v1
      kind: StatefulSet
      metadata:
        name: deploy-sfs-nfs-rw-in
        namespace: default
        labels:
          appgroup: ''
      spec:
        replicas: 2
        selector:
          matchLabels:
            app: deploy-sfs-nfs-rw-in
        template:
          metadata:
            labels:
              app: deploy-sfs-nfs-rw-in
          spec:
            containers:
              - name: container-0
                image: 'nginx:1.12-alpine-perl'
                volumeMounts:
                  - name: bs-nfs-rw-mountoptionpvc
                    mountPath: /aaa
            imagePullSecrets:
              - name: default-secret
        volumeClaimTemplates:
          - metadata:
              name: bs-nfs-rw-mountoptionpvc
              annotations:
                volume.beta.kubernetes.io/storage-class: nfs-rw
                volume.beta.kubernetes.io/storage-provisioner: flexvolume-huawei.com/fuxinfs
            spec:
              accessModes:
                - ReadWriteMany
              resources:
                requests:
                  storage: 1Gi
        serviceName: wwww

   .. table:: **Table 2** Key parameters

      +-------------------------------------------+-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | Parent Parameter                          | Parameter   | Description                                                                                                                        |
      +===========================================+=============+====================================================================================================================================+
      | metadata                                  | name        | Name of the created workload.                                                                                                      |
      +-------------------------------------------+-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | spec.template.spec.containers             | image       | Image of the workload.                                                                                                             |
      +-------------------------------------------+-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | spec.template.spec.containers.volumeMount | mountPath   | Mount path in the container. In this example, the mount path is **/tmp**.                                                          |
      +-------------------------------------------+-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | spec                                      | serviceName | Service corresponding to the workload. For details about how to create a Service, see :ref:`Creating a StatefulSet <cce_10_0048>`. |
      +-------------------------------------------+-------------+------------------------------------------------------------------------------------------------------------------------------------+

   .. note::

      **spec.template.spec.containers.volumeMounts.name** and **spec.volumeClaimTemplates.metadata.name** must be consistent because they have a mapping relationship.

#. Run the following command to create the pod:

   **kubectl create -f sfs-deployment-example.yaml**

   After the creation is complete, log in to the CCE console. In the navigation pane, choose **Resource Management** > **Storage** > **SFS**. Click the PVC name. On the PVC details page, you can view the binding relationship between SFS and PVC.
