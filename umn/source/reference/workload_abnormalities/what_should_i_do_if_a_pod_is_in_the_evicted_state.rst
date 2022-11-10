:original_name: cce_faq_00199.html

.. _cce_faq_00199:

What Should I Do If a Pod Is in the Evicted State?
==================================================

Problem
-------

Workload pods in the cluster fail and are being redeployed constantly.

After the following command is run, the command output shows that many pods are in the evicted state.

.. code-block::

   kubectl get pods

Possible Cause
--------------

When a node is abnormal, Kubernetes evicts pods on the node. This problem is often caused by insufficient resources.

Solution
--------

Check resource usage and locate the fault.

Run the following command to delete the evicted pods:

.. code-block::

   kubectl get pods | grep Evicted | awk '{print $1}' | xargs kubectl delete pod

Reference
---------

`Kubelet does not delete evicted pods <https://github.com/kubernetes/kubernetes/issues/55051>`__
