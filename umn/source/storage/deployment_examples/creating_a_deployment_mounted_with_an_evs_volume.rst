:original_name: cce_10_0257.html

.. _cce_10_0257:

Creating a Deployment Mounted with an EVS Volume
================================================

Scenario
--------

After an EVS volume is created or imported to CCE, you can mount it to a workload.

.. important::

   EVS disks cannot be attached across AZs. Before mounting a volume, you can run the **kubectl get pvc** command to query the available PVCs in the AZ where the current cluster is located.

Prerequisites
-------------

You have created a CCE cluster and installed the CSI plug-in (:ref:`everest <cce_10_0066>`) in the cluster.

Notes and Constraints
---------------------

The following configuration example applies to clusters of Kubernetes 1.15 or later.

Using EVS Volumes for Deployments
---------------------------------

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

#. Run the following command to create the workload:

   **kubectl create -f evs-deployment-example.yaml**

Using EVS Volumes for StatefulSets
----------------------------------

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Run the following commands to configure the **evs-statefulset-example.yaml** file, which is used to create a Deployment.

   **touch** **evs-statefulset-example.yaml**

   **vi** **evs-statefulset-example.yaml**

   Mounting an EVS volume to a StatefulSet (PVC template-based, non-shared volume):

   **Example YAML:**

   .. code-block::

      apiVersion: apps/v1
      kind: StatefulSet
      metadata:
        name: evs-statefulset-example
        namespace: default
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: evs-statefulset-example
        template:
          metadata:
            labels:
              app: evs-statefulset-example
          spec:
            containers:
              - name: container-0
                image: 'nginx:latest'
                volumeMounts:
                  - name: pvc-evs-auto-example
                    mountPath: /tmp
            restartPolicy: Always
            imagePullSecrets:
              - name: default-secret
        volumeClaimTemplates:
          - metadata:
              name: pvc-evs-auto-example
              namespace: default
              labels:
                failure-domain.beta.kubernetes.io/region: eu-de
                failure-domain.beta.kubernetes.io/zone:
              annotations:
                everest.io/disk-volume-type: SAS
            spec:
              accessModes:
                - ReadWriteOnce
              resources:
                requests:
                  storage: 10Gi
              storageClassName: csi-disk
        serviceName: evs-statefulset-example-headless
        updateStrategy:
          type: RollingUpdate

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

#. Run the following command to create the workload:

   **kubectl create -f evs-statefulset-example.yaml**

Verifying Persistent Storage of an EVS Volume
---------------------------------------------

#. Query the pod and EVS files of the deployed workload (for example, **evs-statefulset-example**).

   a. Run the following command to query the pod name of the workload:

      .. code-block::

         kubectl get po | grep evs-statefulset-example

      Expected outputs:

      .. code-block::

         evs-statefulset-example-0   1/1     Running   0          22h

   b. Run the following command to check whether an EVS volume is mounted to the **/tmp** directory:

      .. code-block::

         kubectl exec evs-statefulset-example-0 -- df tmp

      Expected outputs:

      .. code-block::

         /dev/sda        10255636 36888  10202364   1% /tmp

#. Run the following command to create a file named **test** in the **/tmp** directory:

   .. code-block::

      kubectl exec evs-statefulset-example-0 -- touch /tmp/test

#. Run the following command to view the file in the **/tmp** directory:

   .. code-block::

      kubectl exec evs-statefulset-example-0 -- ls -l /tmp

   Expected outputs:

   .. code-block::

      -rw-r--r-- 1 root root     0 Jun  1 02:50 test

#. Run the following command to delete the pod named **evs-statefulset-example-0**:

   .. code-block::

      kubectl delete po evs-statefulset-example-0

#. Check whether the file still exists after the pod is rebuilt.

   a. Run the following command to query the name of the rebuilt pod:

      .. code-block::

         kubectl get po

      Expected outputs:

      .. code-block::

         evs-statefulset-example-0   1/1     Running   0          2m

   b. Run the following command to view the file in the **/tmp** directory:

      .. code-block::

         kubectl exec evs-statefulset-example-0 -- ls -l /tmp

      Expected outputs:

      .. code-block::

         -rw-r--r-- 1 root root     0 Jun  1 02:50 test

   c. The **test** file still exists after the pod is rebuilt, indicating that the data in the EVS volume can be persistently stored.
