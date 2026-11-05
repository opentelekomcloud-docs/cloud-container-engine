:original_name: cce_10_0964.html

.. _cce_10_0964:

Automatically Assigning an EIP to Each Node in a Node Pool
==========================================================

To automate EIP binding, enable the automatic EIP binding option when creating a node pool. This will automatically assign an EIP to each new node in the cluster for Internet access.

.. _cce_10_0964__section20258112710124:

EIP Billing and Lifecycle
-------------------------

When an EIP is automatically assigned from a node pool, its lifecycle matches the node to which the EIP is bound by default. If you bind a non-yearly/monthly EIP to a node (ECS), CCE will configure a release policy for the EIP with the node. This means the EIP will be automatically deleted when the node is deleted. For details, see :ref:`Table 1 <cce_10_0964__table18102161112144>`. To prevent automatic deletion of an EIP bound to a node when the node is deleted, modify the EIP release policy on the ECS console. For details, see :ref:`4 <cce_10_0964__li1310820182542>`.

.. _cce_10_0964__table18102161112144:

.. table:: **Table 1** Comparison between billing modes and EIP lifecycle

   +-------------------+-------------------------+---------------------------------------------------------------------------------------------------------+
   | Node Billing Mode | EIP Bandwidth Billed By | EIP Billing and Lifecycle                                                                               |
   +===================+=========================+=========================================================================================================+
   | Pay-per-use       | Traffic                 | The EIP is billed on a pay-per-use basis and released with the node by default.                         |
   +-------------------+-------------------------+---------------------------------------------------------------------------------------------------------+
   |                   | Bandwidth               | The EIP is billed on a pay-per-use basis and released with the node by default.                         |
   +-------------------+-------------------------+---------------------------------------------------------------------------------------------------------+
   | Yearly/Monthly    | Traffic                 | The EIP is billed on a pay-per-use basis and released with the node by default.                         |
   +-------------------+-------------------------+---------------------------------------------------------------------------------------------------------+
   |                   | Bandwidth               | The EIP is billed on a yearly/monthly basis and is not automatically released with the node by default. |
   +-------------------+-------------------------+---------------------------------------------------------------------------------------------------------+

Prerequisites
-------------

-  A cluster of v1.21.11-r0, v1.23.9-r0, v1.25.4-r0, or later is available.
-  Ensure the target node pool is neither a BMS/remote edge pool nor billed on a yearly/monthly basis. Otherwise, EIPs cannot be automatically assigned.

Procedure
---------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Nodes**. Then, click **Create Node Pool** in the upper right corner of the page. Configure **EIP** in the **Network Settings** area.

   The default value of **EIP** is **Do not use**. If you select **Auto assign**, configure the following parameters:

   -  **EIP Type**

      -  **Dynamic BGP**: In a network using dynamic BGP, changes can be quickly addressed by adjusting configurations via the routing protocol, ensuring stability and optimal performance.
      -  Mail BGP: EIPs with port 25, 465, or 587 enabled are used for email services.

   -  **Bandwidth Size**: Select the bandwidth size (in Mbit/s) based on service requirements.

#. Configure other node pool parameters and click **Submit**. For more details, see :ref:`Creating a Node Pool <cce_10_0012>`.

   An EIP will be automatically assigned to each node newly added to this node pool. The lifecycle of such an EIP matches the node to which the EIP is bound. When a node is deleted, a non-yearly/monthly EIP bound to the node will be automatically deleted. For details, see :ref:`Table 1 <cce_10_0964__table18102161112144>`.

#. .. _cce_10_0964__li1310820182542:

   To prevent automatic deletion of an EIP bound to a node when the node is deleted, modify the EIP release policy on the ECS console.
