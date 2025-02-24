:original_name: cce_faq_00210.html

.. _cce_faq_00210:

What Should I Do If a Pod Remains in the Terminating State?
===========================================================

Symptom
-------

When obtaining workloads in a namespace, you may come across pods that are in the **Terminating** state.

For example, if you use the command below to obtain pods in the **aos** namespace, you may notice that some pods are in the **Terminating** state:

.. code-block::

   #kubectl get pod -n aos
   NAME                              READY      STATUS         RESTARTS   AGE
   aos-apiserver-5f8f5b5585-s9l92     1/1       Terminating    0          3d1h
   aos-cmdbserver-789bf5b497-6rwrg    1/1       Running        0          3d1h
   aos-controller-545d78bs8d-vm6j9    1/1       Running        3          3d1h

Running **kubectl delete pods <podname> -n <namespace>** cannot delete the pods.

.. code-block::

   kubectl delete pods aos-apiserver-5f8f5b5585-s9l92 -n aos

Possible Causes
---------------

The following lists some possible causes:

-  **The node that runs a terminating pod is abnormal.** When a node is unavailable, CCE migrates pods on the node and sets the pods running on the node to the **Terminating** state.

   After the node is restored, the pods in the **Terminating** state will be automatically deleted.

-  **A container is unresponsive.** If a container within a pod fails to respond to the SIGTERM signal while being terminated, the pod can become stuck in the **Terminating** state.

-  **There are unfinished requests or resource occupation within a pod.** If a process within a pod continues to run for an extended period, it may prevent the pod from being terminated and cause it to enter the **Terminating** state.

-  **A pod has finalizers specified.** Finalizers are used to clean up resources before they are deleted. If a pod has finalizers specified and the cleanup process is paused or unresponsive, the pod will remain in the **Terminating** state.

-  **A pod has terminationGracePeriodSeconds configured.** Once a graceful exit time is set for a pod, it will enter the **Terminating** state upon termination and be automatically deleted after exiting gracefully.

Solution
--------

.. note::

   Before forcibly deleting a pod, it is important to consider the potential risks to your services. This is especially true for StatefulSet pods, because there is a higher chance of data inconsistency and abnormal container exits. Take the time to evaluate these risks before proceeding with the operation. For details, see `Force Delete StatefulSet Pods <https://kubernetes.io/docs/tasks/run-application/force-delete-stateful-set-pod/#statefulset-considerations>`__.

Run the following command to forcibly delete the pods created in any ways:

.. code-block::

   kubectl delete pod <pod>  -n <namespace> --grace-period=0 --force

Run the following command to delete the terminating pod described at the very beginning of this section:

.. code-block::

   kubectl delete pod aos-apiserver-5f8f5b5585-s9l92 -n aos --grace-period=0 --force
