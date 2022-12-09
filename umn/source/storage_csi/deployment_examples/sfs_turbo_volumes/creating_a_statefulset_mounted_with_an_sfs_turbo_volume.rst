:original_name: cce_01_0273.html

.. _cce_01_0273:

Creating a StatefulSet Mounted with an SFS Turbo Volume
=======================================================

Scenario
--------

CCE allows you to use an existing SFS Turbo volume to create a StatefulSet.

Prerequisites
-------------

You have created a CCE cluster and installed the CSI plug-in (:ref:`everest <cce_01_0066>`) in the cluster.

Notes and Constraints
---------------------

The following configuration example applies to clusters of Kubernetes 1.15 or later.

Procedure
---------

#. Create an SFS Turbo volume and record the volume name.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_01_0107>`.

#. Create a YAML file for creating the workload. Assume that the file name is **sfsturbo-statefulset-example.yaml**.

   **touch sfsturbo-statefulset-example.yaml**

   **vi sfsturbo-statefulset-example.yaml**

   Configuration example:

   .. code-block::

      apiVersion: apps/v1
      kind: StatefulSet
      metadata:
        name: sfsturbo-statefulset-example
        namespace: default
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: sfsturbo-statefulset-example
        template:
          metadata:
            labels:
              app: sfsturbo-statefulset-example
          spec:
            volumes:
            - name: pvc-sfsturbo-example
              persistentVolumeClaim:
                claimName: pvc-sfsturbo-example
            containers:
            - name: container-0
              image: 'nginx:latest'
              volumeMounts:
                - name: pvc-sfsturbo-example
                  mountPath: /tmp
            restartPolicy: Always
            imagePullSecrets:
            - name: default-secret
        serviceName: sfsturbo-statefulset-example-headless
        updateStrategy:
          type: RollingUpdate

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
      | serviceName | Service corresponding to the workload. For details about how to create a Service, see :ref:`Creating a StatefulSet <cce_01_0048>`. |
      +-------------+------------------------------------------------------------------------------------------------------------------------------------+
      | claimName   | Name of an existing PVC.                                                                                                           |
      +-------------+------------------------------------------------------------------------------------------------------------------------------------+

   .. note::

      **spec.template.spec.containers.volumeMounts.name** and **spec.template.spec.volumes.name** must be consistent because they have a mapping relationship.

#. Create the StatefulSet.

   **kubectl create -f sfsturbo-statefulset-example.yaml**

Verifying Persistent Storage of an SFS Turbo Volume
---------------------------------------------------

#. Query the pod and SFS Turbo volume of the deployed workload (for example, **sfsturbo-statefulset-example**).

   a. Run the following command to query the pod name of the workload:

      .. code-block::

         kubectl get po | grep sfsturbo-statefulset-example

      Expected outputs:

      .. code-block::

         sfsturbo-statefulset-example-0   1/1     Running   0          2m5s

   b. Run the following command to check whether an SFS Turbo volume is mounted to the **/tmp** directory:

      .. code-block::

         kubectl exec sfsturbo-statefulset-example-0 -- mount|grep /tmp

      Expected outputs:

      .. code-block::

         192.168.0.108:/ on /tmp type nfs (rw,relatime,vers=3,rsize=1048576,wsize=1048576,namlen=255,hard,nolock,noresvport,proto=tcp,timeo=600,retrans=2,sec=sys,mountaddr=192.168.0.108,mountvers=3,mountport=20048,mountproto=tcp,local_lock=all,addr=192.168.0.108)

#. Run the following command to create a file named **test** in the **/tmp** directory:

   .. code-block::

      kubectl exec sfsturbo-statefulset-example-0 -- touch /tmp/test

#. Run the following command to view the file in the **/tmp** directory:

   .. code-block::

      kubectl exec sfsturbo-statefulset-example-0 -- ls -l /tmp

   Expected outputs:

   .. code-block::

      -rw-r--r-- 1 root root     0 Jun  1 02:50 test

#. Run the following command to delete the pod named **sfsturbo-statefulset-example-0**:

   .. code-block::

      kubectl delete po sfsturbo-statefulset-example-0

#. Check whether the file still exists after the pod is rebuilt.

   a. Run the following command to query the name of the rebuilt pod:

      .. code-block::

         kubectl get po

      Expected outputs:

      .. code-block::

         sfsturbo-statefulset-example-0   1/1     Running   0          2m

   b. Run the following command to view the file in the **/tmp** directory:

      .. code-block::

         kubectl exec sfsturbo-statefulset-example-0 -- ls -l /tmp

      Expected outputs:

      .. code-block::

         -rw-r--r-- 1 root root     0 Jun  1 02:50 test

      The **test** file still exists after the pod is rebuilt, indicating that the data in the SFS Turbo volume can be persistently stored.
