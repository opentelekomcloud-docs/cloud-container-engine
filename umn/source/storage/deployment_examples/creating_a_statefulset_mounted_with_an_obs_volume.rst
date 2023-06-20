:original_name: cce_10_0268.html

.. _cce_10_0268:

Creating a StatefulSet Mounted with an OBS Volume
=================================================

Scenario
--------

CCE allows you to use an existing OBS volume to create a StatefulSet through a PVC.

Prerequisites
-------------

You have created a cluster and installed the CSI plug-in (:ref:`everest <cce_10_0066>`) in the cluster.

Notes and Constraints
---------------------

The following configuration example applies to clusters of Kubernetes 1.15 or later.

Procedure
---------

#. Create an OBS volume by referring to :ref:`PersistentVolumeClaims (PVCs) <cce_10_0378>` and obtain the PVC name.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create a YAML file for creating the workload. Assume that the file name is **obs-statefulset-example.yaml**.

   **touch obs-statefulset-example.yaml**

   **vi obs-statefulset-example.yaml**

   Configuration example:

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
        template:
          metadata:
            labels:
              app: obs-statefulset-example
          spec:
            volumes:
            - name: pvc-obs-example
              persistentVolumeClaim:
                claimName: pvc-obs-example
            containers:
            - name: container-0
              image: 'nginx:latest'
              volumeMounts:
                - name: pvc-obs-example
                  mountPath: /tmp
            restartPolicy: Always
            imagePullSecrets:
            - name: default-secret
        serviceName: obs-statefulset-example-headless    # Name of the headless Service

   .. table:: **Table 1** Key parameters

      +-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter   | Description                                                                                                                        |
      +=============+====================================================================================================================================+
      | replicas    | Number of pods.                                                                                                                    |
      +-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | name        | Name of the new workload.                                                                                                          |
      +-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | image       | Image used by the workload.                                                                                                        |
      +-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | mountPath   | Mount path of a container.                                                                                                         |
      +-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | serviceName | Service corresponding to the workload. For details about how to create a Service, see :ref:`Creating a StatefulSet <cce_10_0048>`. |
      +-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | claimName   | Name of an existing PVC.                                                                                                           |
      +-------------+------------------------------------------------------------------------------------------------------------------------------------+

   Example of mounting an OBS volume to a StatefulSet (PVC template-based, dedicated volume):

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
        template:
          metadata:
            labels:
              app: obs-statefulset-example
          spec:
            containers:
              - name: container-0
                image: 'nginx:latest'
                volumeMounts:
                  - name: pvc-obs-auto-example
                    mountPath: /tmp
            restartPolicy: Always
            imagePullSecrets:
              - name: default-secret
        volumeClaimTemplates:
          - metadata:
              name: pvc-obs-auto-example
              namespace: default
              annotations:
                everest.io/obs-volume-type: STANDARD
            spec:
              accessModes:
                - ReadWriteMany
              resources:
                requests:
                  storage: 1Gi
              storageClassName: csi-obs
        serviceName: obs-statefulset-example-headless

#. Create a StatefulSet.

   **kubectl create -f obs-statefulset-example.yaml**

Verifying Persistent Storage of an OBS Volume
---------------------------------------------

#. Query the pod and OBS volume of the deployed workload (for example, **obs-statefulset-example**).

   a. Run the following command to query the pod name of the workload:

      .. code-block::

         kubectl get po | grep obs-statefulset-example

      Expected outputs:

      .. code-block::

         obs-statefulset-example-0   1/1     Running   0          2m5s

   b. Run the following command to check whether an OBS volume is mounted to the **/tmp** directory:

      .. code-block::

         kubectl exec obs-statefulset-example-0 -- mount|grep /tmp

      Expected outputs:

      .. code-block::

         s3fs on /tmp type fuse.s3fs (rw,nosuid,nodev,relatime,user_id=0,group_id=0,allow_other)

#. Run the following command to create a file named **test** in the **/tmp** directory:

   .. code-block::

      kubectl exec obs-statefulset-example-0 -- touch /tmp/test

#. Run the following command to view the file in the **/tmp** directory:

   .. code-block::

      kubectl exec obs-statefulset-example-0 -- ls -l /tmp

   Expected outputs:

   .. code-block::

      -rw-r--r-- 1 root root     0 Jun  1 02:50 test

#. Run the following command to delete the pod named **obs-statefulset-example-0**:

   .. code-block::

      kubectl delete po obs-statefulset-example-0

#. Check whether the file still exists after the pod is rebuilt.

   a. Run the following command to query the name of the rebuilt pod:

      .. code-block::

         kubectl get po

      Expected outputs:

      .. code-block::

         obs-statefulset-example-0   1/1     Running   0          2m

   b. Run the following command to view the file in the **/tmp** directory:

      .. code-block::

         kubectl exec obs-statefulset-example-0 -- ls -l /tmp

      Expected outputs:

      .. code-block::

         -rw-r--r-- 1 root root     0 Jun  1 02:50 test

   c. The **test** file still exists after the pod is rebuilt, indicating that the data in the OBS volume can be persistently stored.
