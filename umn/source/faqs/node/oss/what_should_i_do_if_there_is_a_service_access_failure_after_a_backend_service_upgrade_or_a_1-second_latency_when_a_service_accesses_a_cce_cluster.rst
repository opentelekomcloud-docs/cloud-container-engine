:original_name: cce_faq_00409.html

.. _cce_faq_00409:

What Should I Do If There Is a Service Access Failure After a Backend Service Upgrade or a 1-Second Latency When a Service Accesses a CCE Cluster?
==================================================================================================================================================

Symptom
-------

If the kernel version of a node is earlier than 5.9 and a CCE cluster runs in IPVS forwarding mode, there may be a service access failure after a backend service upgrade or a 1-second latency when a service accesses the CCE cluster. This is caused by a bug in reusing Kubernetes IPVS connections.

IPVS Connection Reuse Parameters
--------------------------------

The port reuse policy of IPVS is determined by the kernel parameter **net.ipv4.vs.conn_reuse_mode**.

#. If **net.ipv4.vs.conn_reuse_mode** is set to **0**, IPVS does not reschedule a new connection, but forwards the new connection to the original RS (IPVS backend).
#. If **net.ipv4.vs.conn_reuse_mode** is set to **1**, IPVS reschedules a new connection.

Problems Caused by IPVS Connection Reuse
----------------------------------------

-  .. _cce_faq_00409__en-us_topic_0000001250194017_li4733164313217:

   **Problem 1**

   If **net.ipv4.vs.conn_reuse_mode** is set to **0**, IPVS does not proactively schedule new connections with port reuse or trigger any connection termination or drop operations. Data packets of the new connections will be directly forwarded to the previously used backend pod. If the backend pod has been deleted or recreated, an exception occurs. However, according to the current implementation logic, in a high-concurrency service access scenario, connection requests for port reuse are continuously forwarded, while kube-proxy did not delete the old ones, resulting in a service access failure.

-  .. _cce_faq_00409__en-us_topic_0000001250194017_li6733154317325:

   **Problem 2**

   If **net.ipv4.vs.conn_reuse_mode** is set to **1** and the source port is the same as that of a previous connection in a high-concurrency scenario, the connection is not reused but rescheduled. According to the processing logic of ip_vs_in(), if **net.ipv4.vs.conntrack** is enabled, the first SYN packet is dropped. As a result, the SYN packet will be retransmitted, leading to a 1-second latency, and the performance deteriorates.

Community Settings and Impact on CCE Clusters
---------------------------------------------

The default value of **net.ipv4.vs.conn_reuse_mode** on a node is **1**. However, the Kubernetes kube-proxy resets this parameter.

+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Cluster Version       | kube-proxy Action                                                                                                                                                                                                                                                                                                                           | Impact on CCE Cluster                                                                                                                                                                                                                                                                                                                                                                                   |
+=======================+=============================================================================================================================================================================================================================================================================================================================================+=========================================================================================================================================================================================================================================================================================================================================================================================================+
| 1.17 or earlier       | By default, kube-proxy sets **net.ipv4.vs.conn_reuse_mode** to **0**. For details, see `Fix IPVS low throughput issue <https://github.com/kubernetes/kubernetes/pull/71114>`__.                                                                                                                                                             | If CCE clusters of 1.17 or earlier versions use the IPVS service forwarding mode, kube-proxy will set the **net.ipv4.vs.conn_reuse_mode** value of all nodes to **0** by default. This causes :ref:`Problem 1 <cce_faq_00409__en-us_topic_0000001250194017_li4733164313217>`: The RS cannot be removed when the port is reused.                                                                         |
+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| 1.19 or later         | kube-proxy sets the value of **net.ipv4.vs.conn_reuse_mode** based on the kernel version. For details, see `ipvs: only attempt setting of sysctlconnreuse on supported kernels <https://github.com/kubernetes/kubernetes/pull/88541>`__.                                                                                                    | If the IPVS service forwarding mode is used in CCE clusters of 1.19.16-r0 or later, the value of **net.ipv4.vs.conn_reuse_mode** varies with the kernel versions of node OSs.                                                                                                                                                                                                                           |
|                       |                                                                                                                                                                                                                                                                                                                                             |                                                                                                                                                                                                                                                                                                                                                                                                         |
|                       | -  If the kernel version is later than 4.1, kube-proxy will set **net.ipv4.vs.conn_reuse_mode** to **0**.                                                                                                                                                                                                                                   | -  For a node running EulerOS 2.5, if the kernel version is earlier than 4.1, kube-proxy will keep **net.ipv4.vs.conn_reuse_mode** at **1**. This results in :ref:`Problem 2 <cce_faq_00409__en-us_topic_0000001250194017_li6733154317325>`, which is, there is a 1-second latency in the high-concurrency scenarios.                                                                                   |
|                       | -  In other cases, the default value **1** will be retained.                                                                                                                                                                                                                                                                                | -  For a node running EulerOS 2.9, if the kernel version is too early, kube-proxy will set **net.ipv4.vs.conn_reuse_mode** to **0**. This results in :ref:`Problem 1 <cce_faq_00409__en-us_topic_0000001250194017_li4733164313217>`. To resolve this problem, upgrade the kernel version. For details, see :ref:`Rectification Plan <cce_faq_00409__en-us_topic_0000001250194017_section059433819273>`. |
|                       |                                                                                                                                                                                                                                                                                                                                             | -  For a node running HCE OS 2.0 or Ubuntu 22.04, if the kernel version is later than 5.9, the problem has been resolved.                                                                                                                                                                                                                                                                               |
|                       | .. note::                                                                                                                                                                                                                                                                                                                                   |                                                                                                                                                                                                                                                                                                                                                                                                         |
|                       |                                                                                                                                                                                                                                                                                                                                             |                                                                                                                                                                                                                                                                                                                                                                                                         |
|                       |    This issue has been resolved in Linux kernel 5.9. Since Kubernetes 1.22, kube-proxy does not modify the **net.ipv4.vs.conn_reuse_mode** parameter of nodes that use the kernel 5.9 or later. For details, see `Don't set sysctl net.ipv4.vs.conn_reuse_mode for kernels >=5.9 <https://github.com/kubernetes/kubernetes/pull/102122>`__. |                                                                                                                                                                                                                                                                                                                                                                                                         |
+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Suggestions
-----------

Evaluate the impact of these problems. If they affect your services, take the following measures:

#. Use an OS that is not affected by the preceding issues, for example, HCE OS 2.0 or Ubuntu 22.04. The newly created nodes which run EulerOS 2.9 are not affected by the preceding issues. Upgrade the earlier kernel versions used by existing nodes to the fixed version. For details, see :ref:`Rectification Plan <cce_faq_00409__en-us_topic_0000001250194017_section059433819273>`.
#. Use a cluster whose forwarding mode is iptables.

.. _cce_faq_00409__en-us_topic_0000001250194017_section059433819273:

Rectification Plan
------------------

If you use a node running EulerOS 2.9, check whether the kernel version meets the requirements. If the kernel version of the node is too early, reset the node or create a new one.

The following kernel versions are recommended:

-  x86: 4.18.0-147.5.1.6.h686.eulerosv2r9.x86_64
-  Arm: 4.19.90-vhulk2103.1.0.h584.eulerosv2r9.aarch64

Kubernetes community issue: https://github.com/kubernetes/kubernetes/issues/81775
