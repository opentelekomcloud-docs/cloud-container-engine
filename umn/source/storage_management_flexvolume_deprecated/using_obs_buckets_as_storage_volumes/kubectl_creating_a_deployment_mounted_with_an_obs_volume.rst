:original_name: cce_10_0327.html

.. _cce_10_0327:

(kubectl) Creating a Deployment Mounted with an OBS Volume
==========================================================

Scenario
--------

After an OBS volume is created or imported to CCE, you can mount the volume to a workload.

Notes and Constraints
---------------------

The following configuration example applies to clusters of Kubernetes 1.13 or earlier.

Procedure
---------

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Run the following commands to configure the **obs-deployment-example.yaml** file, which is used to create a pod.

   **touch obs-deployment-example.yaml**

   **vi obs-deployment-example.yaml**

   Example of mounting an OBS volume to a Deployment (PVC-based, shared volume):

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
         name: obs-deployment-example                       # Workload name
        namespace: default
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: obs-deployment-example
        template:
          metadata:
            labels:
              app: obs-deployment-example
          spec:
            containers:
            - image: nginx
              name: container-0
              volumeMounts:
              - mountPath: /tmp                       # Mount path
                name: pvc-obs-example
            restartPolicy: Always
            imagePullSecrets:
              - name: default-secret
            volumes:
            - name: pvc-obs-example
              persistentVolumeClaim:
                claimName: pvc-obs-auto-example       # PVC name

   .. table:: **Table 1** Key parameters

      ========= ===========================================
      Parameter Description
      ========= ===========================================
      name      Name of the pod to be created.
      app       Name of the application running in the pod.
      mountPath Mount path in the container.
      ========= ===========================================

   .. note::

      **spec.template.spec.containers.volumeMounts.name** and **spec.template.spec.volumes.name** must be consistent because they have a mapping relationship.

   Example of mounting an OBS volume to a StatefulSet (PVC template-based, dedicated volume):

   **Example YAML:**

   .. code-block::

      apiVersion: apps/v1
      kind: StatefulSet
      metadata:
        name: deploy-obs-standard-in
        namespace: default
        generation: 1
        labels:
          appgroup: ''
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: deploy-obs-standard-in
        template:
          metadata:
            labels:
              app: deploy-obs-standard-in
            annotations:
              metrics.alpha.kubernetes.io/custom-endpoints: '[{"api":"","path":"","port":"","names":""}]'
              pod.alpha.kubernetes.io/initialized: 'true'
          spec:
            containers:
              - name: container-0
                image: 'nginx:1.12-alpine-perl'
                env:
                  - name: PAAS_APP_NAME
                    value: deploy-obs-standard-in
                  - name: PAAS_NAMESPACE
                    value: default
                  - name: PAAS_PROJECT_ID
                    value: a2cd8e998dca42e98a41f596c636dbda
                resources: {}
                volumeMounts:
                  - name: obs-bs-standard-mountoptionpvc
                    mountPath: /tmp
                terminationMessagePath: /dev/termination-log
                terminationMessagePolicy: File
                imagePullPolicy: IfNotPresent
            restartPolicy: Always
            terminationGracePeriodSeconds: 30
            dnsPolicy: ClusterFirst
            securityContext: {}
            imagePullSecrets:
              - name: default-secret
            affinity: {}
            schedulerName: default-scheduler
        volumeClaimTemplates:
          - metadata:
              name: obs-bs-standard-mountoptionpvc
              annotations:
                volume.beta.kubernetes.io/storage-class: obs-standard
                volume.beta.kubernetes.io/storage-provisioner: flexvolume-huawei.com/fuxiobs
            spec:
              accessModes:
                - ReadWriteMany
              resources:
                requests:
                  storage: 1Gi
        serviceName: wwww
        podManagementPolicy: OrderedReady
        updateStrategy:
          type: RollingUpdate
        revisionHistoryLimit: 10

   .. table:: **Table 2** Key parameters

      +-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter   | Description                                                                                                                        |
      +=============+====================================================================================================================================+
      | name        | Name of the created workload.                                                                                                      |
      +-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | image       | Image of the workload.                                                                                                             |
      +-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | mountPath   | Mount path in the container. In this example, the volume is mounted to the **/tmp** directory.                                     |
      +-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | serviceName | Service corresponding to the workload. For details about how to create a Service, see :ref:`Creating a StatefulSet <cce_10_0048>`. |
      +-------------+------------------------------------------------------------------------------------------------------------------------------------+

   .. note::

      **spec.template.spec.containers.volumeMounts.name** and **spec.volumeClaimTemplates.metadata.name** must be consistent because they have a mapping relationship.

#. Run the following command to create the pod:

   **kubectl create -f obs-deployment-example.yaml**

   After the creation is complete, choose **Storage** > **OBS** on the CCE console and click the PVC name. On the PVC details page, you can view the binding relationship between the OBS service and the PVC.
