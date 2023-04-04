:original_name: cce_10_0314.html

.. _cce_10_0314:

(kubectl) Creating a Pod Mounted with an EVS Volume
===================================================

Scenario
--------

After an EVS volume is created or imported to CCE, you can mount it to a workload.

.. important::

   EVS disks cannot be attached across AZs. Before mounting a volume, you can run the **kubectl get pvc** command to query the available PVCs in the AZ where the current cluster is located.

Notes and Constraints
---------------------

The following configuration example applies to clusters of Kubernetes 1.13 or earlier.

Procedure
---------

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Run the following commands to configure the **evs-deployment-example.yaml** file, which is used to create a Deployment.

   **touch evs-deployment-example.yaml**

   **vi evs-deployment-example.yaml**

   Example of mounting an EVS volume to a Deployment (PVC-based, shared volume):

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: evs-deployment-example
        namespace: default
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: evs-deployment-example
        template:
          metadata:
            labels:
              app: evs-deployment-example
          spec:
            containers:
            - image: nginx
              name: container-0
              volumeMounts:
              - mountPath: /tmp
                name: pvc-evs-example
            imagePullSecrets:
              - name: default-secret
            restartPolicy: Always
            volumes:
            - name: pvc-evs-example
              persistentVolumeClaim:
                claimName: pvc-evs-auto-example

   .. table:: **Table 1** Key parameters

      +--------------------------------------------------+-----------+------------------------------------------------------------------------------------------------+
      | Parent Parameter                                 | Parameter | Description                                                                                    |
      +==================================================+===========+================================================================================================+
      | spec.template.spec.containers.volumeMounts       | name      | Name of the volume mounted to the container.                                                   |
      +--------------------------------------------------+-----------+------------------------------------------------------------------------------------------------+
      | spec.template.spec.containers.volumeMounts       | mountPath | Mount path of the container. In this example, the volume is mounted to the **/tmp** directory. |
      +--------------------------------------------------+-----------+------------------------------------------------------------------------------------------------+
      | spec.template.spec.volumes                       | name      | Name of the volume.                                                                            |
      +--------------------------------------------------+-----------+------------------------------------------------------------------------------------------------+
      | spec.template.spec.volumes.persistentVolumeClaim | claimName | Name of an existing PVC.                                                                       |
      +--------------------------------------------------+-----------+------------------------------------------------------------------------------------------------+

   .. note::

      **spec.template.spec.containers.volumeMounts.name** and **spec.template.spec.volumes.name** must be consistent because they have a mapping relationship.

   Mounting an EVS volume to a StatefulSet (PVC template-based, non-shared volume):

   **Example YAML:**

   .. code-block::

      apiVersion: apps/v1
      kind: StatefulSet
      metadata:
        name: deploy-evs-sas-in
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: deploy-evs-sata-in
        template:
          metadata:
            labels:
              app: deploy-evs-sata-in
              failure-domain.beta.kubernetes.io/region: eu-de
              failure-domain.beta.kubernetes.io/zone: eu-de-01
          spec:
            containers:
              - name: container-0
                image: 'nginx:1.12-alpine-perl'
                volumeMounts:
                  - name: bs-sas-mountoptionpvc
                    mountPath: /tmp
            imagePullSecrets:
              - name: default-secret
        volumeClaimTemplates:
          - metadata:
              name: bs-sas-mountoptionpvc
              annotations:
                volume.beta.kubernetes.io/storage-class: sas
                volume.beta.kubernetes.io/storage-provisioner: flexvolume-huawei.com/fuxivol
            spec:
              accessModes:
                - ReadWriteOnce
              resources:
                requests:
                  storage: 10Gi
        serviceName: wwww

   .. table:: **Table 2** Key parameters

      +-------------------------------------------+-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | Parent Parameter                          | Parameter   | Description                                                                                                                        |
      +===========================================+=============+====================================================================================================================================+
      | metadata                                  | name        | Name of the created workload.                                                                                                      |
      +-------------------------------------------+-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | spec.template.spec.containers             | image       | Image of the workload.                                                                                                             |
      +-------------------------------------------+-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | spec.template.spec.containers.volumeMount | mountPath   | Mount path of the container. In this example, the volume is mounted to the **/tmp** directory.                                     |
      +-------------------------------------------+-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | spec                                      | serviceName | Service corresponding to the workload. For details about how to create a Service, see :ref:`Creating a StatefulSet <cce_10_0048>`. |
      +-------------------------------------------+-------------+------------------------------------------------------------------------------------------------------------------------------------+

   .. note::

      **spec.template.spec.containers.volumeMounts.name** and **spec.volumeClaimTemplates.metadata.name** must be consistent because they have a mapping relationship.

#. Run the following command to create the pod:

   **kubectl create -f evs-deployment-example.yaml**

   After the creation is complete, log in to the CCE console. In the navigation pane, choose **Resource Management** > **Storage** > **EVS**. Then, click the PVC name. On the PVC details page, you can view the binding relationship between the EVS volume and the PVC.
