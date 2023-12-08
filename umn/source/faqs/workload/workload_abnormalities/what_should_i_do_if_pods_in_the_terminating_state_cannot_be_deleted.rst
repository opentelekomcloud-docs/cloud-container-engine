:original_name: cce_faq_00210.html

.. _cce_faq_00210:

What Should I Do If Pods in the Terminating State Cannot Be Deleted?
====================================================================

Symptom
-------

When a node is in the Unavailable state, CCE migrates container pods on the node and sets the pods running on the node to the **Terminating** state.

After the node is restored, the pods in the **Terminating** state are automatically deleted.

However, some pods remain in the **Terminating** state.

.. code-block::

   #kubectl get pod -n aos
   NAME                              READY      STATUS         RESTARTS   AGE
   aos-apiserver-5f8f5b5585-s9l92     1/1       Terminating    0          3d1h
   aos-cmdbserver-789bf5b497-6rwrg    1/1       Running        0          3d1h
   aos-controller-545d78bs8d-vm6j9    1/1       Running        3          3d1h

Running **kubectl delete pods <podname> -n <namespace>** cannot delete the pods.

.. code-block::

   kubectl delete pods aos-apiserver-5f8f5b5585-s9l92 -n aos

Solution
--------

You can run the following command to forcibly delete the pods created in any ways:

.. code-block::

   kubectl delete pods <pod> --grace-period=0 --force

Therefore, run the following command to delete the pod:

.. code-block::

   kubectl delete pods aos-apiserver-5f8f5b5585-s9l92 --grace-period=0 --force
