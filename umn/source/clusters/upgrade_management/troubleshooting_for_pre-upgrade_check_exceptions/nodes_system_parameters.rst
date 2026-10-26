:original_name: cce_10_0504.html

.. _cce_10_0504:

Nodes' System Parameters
========================

Check Items
-----------

Check whether the system parameters on your node meet the expectation.

Solutions
---------

-  **Scenario 1**: If the MTU value of the **bond0** network on your BMS node is not the default value **1500**, this check item will fail.

   Non-default parameter settings may lead to service packet loss. Change them back to the default values. Take the following steps:

   #. Disable **bond0**.

      .. code-block::

         sudo ip link set dev bond0 down

      .. caution::

         Disabling **bond0** will interrupt services. Exercise caution when performing this operation.

   #. Change the MTU value to **1500**.

      .. code-block::

         sudo ip link set dev bond0 mtu 1500

   #. Restart **bond0**.

      .. code-block::

         sudo ip link set dev bond0 up

   #. Verify the modification result. (Ensure that the MTU has been updated.)

      .. code-block::

         ip link show dev bond0 | grep mtu

      If the command output is **mtu 1500**, the setting has been modified.

-  **Scenario 2**: If you upgrade the cluster to v1.29 or later, the kernel parameter **nf_contrack_max** is set by kube-proxy. If the value of this parameter is greater than that of **conntrack-min** configured for kube-proxy, this check item will fail.

   You are advised to use the kube-proxy startup parameter **conntrack-min** to ensure that the kernel parameter value is not less than the value configured on the current node after the upgrade.

   #. View the **nf_contrack_max** kernel parameter configuration.

      .. code-block::

         cat /proc/sys/net/netfilter/nf_conntrack_max

   #. Modify the **conntrack-min** of kube-proxy through :ref:`node pool configuration management <cce_10_0652>`. Set it to the value of the **nf_contrack_max** kernel parameter on the current node (which is the value queried in the previous step).
