:original_name: cce_10_0728.html

.. _cce_10_0728:

Taints and Tolerations
======================

Tolerations allow the scheduler to schedule pods to nodes with target taints. Tolerances work with :ref:`node taints <cce_10_0352>`. Each node allows one or more taints. If no tolerance is configured for a pod, the scheduler will schedule the pod based on node taint policies to prevent the pod from being scheduled to an inappropriate node.

The following table shows how taint policies and tolerations affect pod running.

+-----------------------+-------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Taint Policy          | No Taint Toleration Configured                                          | Taint Toleration Configured                                                                                                                                                          |
+=======================+=========================================================================+======================================================================================================================================================================================+
| NoExecute             | -  Pods running on the node will be evicted immediately.                | -  If the tolerance time window **tolerationSeconds** is not specified, pods can run on this node all the time.                                                                      |
|                       | -  Inactive pods will not scheduled to the node.                        | -  If the tolerance time window **tolerationSeconds** is specified, pods still run on the node with taints within the time window. After the time expires, the pods will be evicted. |
+-----------------------+-------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| PreferNoSchedule      | -  Pods running on the node will not be evicted.                        | Pods can run on this node all the time.                                                                                                                                              |
|                       | -  Inactive pods will not scheduled to the node **to the best extend**. |                                                                                                                                                                                      |
+-----------------------+-------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| NoSchedule            | -  Pods running on the node will not be evicted.                        | Pods can run on this node all the time.                                                                                                                                              |
|                       | -  Inactive pods will not scheduled to the node.                        |                                                                                                                                                                                      |
+-----------------------+-------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Configuring Tolerance Policies on the Console
---------------------------------------------

#. Log in to the CCE console.
#. When creating a workload, click **Toleration** in the **Advanced Settings** area.
#. Add a taint tolerance policy.

   .. table:: **Table 1** Parameters for configuring a taint tolerance policy

      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                           |
      +===================================+=======================================================================================================================================================================================================+
      | Taint key                         | Key of a node taint                                                                                                                                                                                   |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Operator                          | -  **Equal**: **Exact match** for the specified taint key (mandatory) and taint value. If the taint value is left blank, all taints with the key the same as the specified taint key will be matched. |
      |                                   | -  **Exists**: **matches only** the nodes with the specified taint key. In this case, the taint value cannot be specified. If the taint key is left blank, all taints will be tolerated.              |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Taint value                       | Taint value specified if the operator is set to **Equal**.                                                                                                                                            |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Taint Policy                      | -  **All**: All taint policies are matched.                                                                                                                                                           |
      |                                   | -  **NoSchedule**: Only the **NoSchedule** taint is matched.                                                                                                                                          |
      |                                   | -  **PreferNoSchedule**: Only the **PreferNoSchedule** taint is matched.                                                                                                                              |
      |                                   | -  **NoExecute**: Only the **NoExecute** taint is matched.                                                                                                                                            |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Toleration Time Window            | **tolerationSeconds**, which is configurable only when **Taint Policy** is set to **NoExecute**.                                                                                                      |
      |                                   |                                                                                                                                                                                                       |
      |                                   | Within the tolerance time window, pods still run on the node with taints. After the time expires, the pods will be evicted.                                                                           |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Default Tolerance Policy
------------------------

Kubernetes automatically adds tolerances for the **node.kubernetes.io/not-ready** and **node.kubernetes.io/unreachable** taints to pods, and sets the tolerance time window (**tolerationSeconds**) to 300s. These default tolerance policies indicate that when either of the preceding taint is added to the node where pods are running, the pods can still run on the node for 5 minutes.

.. note::

   When a DaemonSet pod is created, no tolerance time window will be specified for the tolerances automatically added for the preceding taints. When either of the preceding taints is added to the node where the DaemonSet pod is running, the DaemonSet pod will never be evicted.

.. code-block::

   tolerations:
     - key: node.kubernetes.io/not-ready
       operator: Exists
       effect: NoExecute
       tolerationSeconds: 300
     - key: node.kubernetes.io/unreachable
       operator: Exists
       effect: NoExecute
       tolerationSeconds: 300
