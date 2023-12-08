:original_name: cce_faq_00189.html

.. _cce_faq_00189:

What Should I Do If I Fail to Restart or Create Workloads on a Node After Modifying the Node Specifications?
============================================================================================================

Context
-------

The kubelet option **cpu-manager-policy** defaults to **static**, allowing pods with certain resource characteristics to be granted increased CPU affinity and exclusivity on the node. If you modify CCE node specifications on the ECS console, the original CPU information does not match the new CPU information. As a result, workloads on the node cannot be restarted or created.

For more information, see `Control CPU Management Policies on the Node <https://kubernetes.io/docs/tasks/administer-cluster/cpu-management-policies/>`__.

Impact
------

The clusters that have enabled a CPU management policy will be affected.

Solution
--------

#. Log in to the CCE node (ECS) and delete the **cpu_manager_state** file.

   Example command for the file deletion:

   .. code-block::

      rm -rf /mnt/paas/kubernetes/kubelet/cpu_manager_state

#. Restart the node or kubelet. The following is the kubelet restart command:

   .. code-block::

      systemctl restart kubelet

#. Verify that workloads on the node can be successfully restarted or created.
