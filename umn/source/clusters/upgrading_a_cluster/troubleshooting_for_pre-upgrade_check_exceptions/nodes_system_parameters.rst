:original_name: cce_10_0504.html

.. _cce_10_0504:

Nodes' System Parameters
========================

Check Items
-----------

Check whether the default system parameter settings on your nodes are modified.

Solution
--------

If the MTU value of the **bond0** network on your BMS node is not the default value **1500**, this check item will fail.

Non-default parameter settings may lead to service packet loss. Change them back to the default values. Take the following steps:

#. Disable **bond0**.

   .. code-block::

      sudo ip link set dev bond0 down

   .. caution::

      Disabling **bond0** will interrupt services. Exercise caution when performing this operation.

#. Change the MTU value to **1500**.

   .. code-block::

      sudo ip link set dev bond0 mtu 1500

#. Restart bond0.

   .. code-block::

      sudo ip link set dev bond0 up

#. Verify the modification result. (Ensure that the MTU has been updated.)

   .. code-block::

      ip link show dev bond0 | grep mtu

   If the command output is **mtu 1500**, the setting has been modified.
