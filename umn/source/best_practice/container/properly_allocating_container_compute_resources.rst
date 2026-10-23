:original_name: cce_bestpractice_00002.html

.. _cce_bestpractice_00002:

Properly Allocating Container Compute Resources
===============================================

If a node has sufficient memory resources, a pod on this node can use more memory resources than requested, but no more than the limit. If the memory allocated to a pod exceeds the upper limit, the pod is terminated first. If the pod continuously uses memory resources more than the limit, it will be terminated. If a terminated pod is allowed to be restarted, kubelet will restart it, but other types of runtime errors will occur for the pod.

Scenario 1
----------

The node's memory has reached the memory limit reserved for the node. As a result, OOM killer is triggered.

**Solution**

Scale out the node or migrate the pods on the node to other nodes.

Scenario 2
----------

The upper limit of resources configured for the pod is too low. When the actual usage exceeds the limit, an OOM kill is triggered.

**Solution**

Set a higher upper limit for the workload.

Example
-------

A pod will be created and allocated memory that exceeds the limit. As shown in the configuration file of the pod below, the pod requests 50 MiB of memory and the memory limit is 100 MiB.

Example YAML file (memory-request-limit-2.yaml):

.. code-block::

   apiVersion: v1
   kind: Pod
   metadata:
     name: memory-demo-2
   spec:
     containers:
     - name: memory-demo-2-ctr
       image: vish/stress
       resources:
         requests:
           memory: 50Mi
         limits:
           memory: "100Mi"
       args:
       - -mem-total
       - 250Mi
       - -mem-alloc-size
       - 10Mi
       - -mem-alloc-sleep
       - 1s

The **args** parameters indicate that the pod attempts to request 250 MiB of memory, which exceeds the pod's upper limit (100 MiB).

Creating a pod:

.. code-block::

   kubectl create -f memory-request-limit-2.yaml --namespace=mem-example

Viewing the details about the pod:

.. code-block::

   kubectl get pod memory-demo-2 --namespace=mem-example

In this stage, the pod may be running or killed. If the pod is not killed, repeat the previous command until the pod has been killed.

.. code-block::

   NAME            READY     STATUS      RESTARTS   AGE
   memory-demo-2   0/1       OOMKilled   1          24s

View detailed information about the pod.

.. code-block::

   kubectl get pod memory-demo-2 --output=yaml --namespace=mem-example

This output indicates that the pod is killed because the memory limit is exceeded.

.. code-block::

   lastState:
      terminated:
        containerID: docker://7aae52677a4542917c23b10fb56fcb2434c2e8427bc956065183c1879cc0dbd2
        exitCode: 137
        finishedAt: 2020-02-20T17:35:12Z
        reason: OOMKilled
        startedAt: null

In this example, the pod supports automatic restarts, so kubelet will restart it upon termination. You can run the following command multiple times to see how the pod is stopped and restarted:

.. code-block::

   kubectl get pod memory-demo-2 --namespace=mem-example

The preceding command output indicates how the pod is killed and started back and forth:

.. code-block::

   $ kubectl get pod memory-demo-2 --namespace=mem-example
   NAME            READY     STATUS      RESTARTS   AGE
   memory-demo-2   0/1       OOMKilled   1          37s
   $ kubectl get pod memory-demo-2 --namespace=mem-example
   NAME            READY     STATUS    RESTARTS   AGE
   memory-demo-2   1/1       Running   2          40s

Viewing the historical information of the pod:

.. code-block::

   kubectl describe pod memory-demo-2 --namespace=mem-example

The following command output indicates that the pod is repeatedly killed and started.

.. code-block::

   ... Normal  Created   Created container with id 66a3a20aa7980e61be4922780bf9d24d1a1d8b7395c09861225b0eba1b1f8511
   ... Warning BackOff   Back-off restarting failed container
