:original_name: cce_10_0528.html

.. _cce_10_0528:

Cluster and Node Pool Configurations
====================================

Check Items
-----------

Check whether the **nic-max-above-warm-target** value configured for the network component of the current cluster exceeds the maximum value allowed.

Solution
--------

Determine the scope of impact based on the error information. For example:

.. code-block::

   configuration check failed: [nodepool id(1786cd55-xxxx-xxxx-aac8-0255ac100095): [eni/nic-max-above-warm-target] should be less than or equal to 256, nodepool id(master): [eni/nic-max-above-warm-target] should be less than or equal to 256]. Please make corrections at settings

This error information indicates that there are two abnormal **nic-max-above-warm-target** settings:

-  **nodepool id(1786cd55-xxxx-xxxx-aac8-0255ac100095)** indicates that the node pool with the ID **1786cd55-xxxx-xxxx-aac8-0255ac100095** has an abnormal configuration.
-  **nodepool id(master)** indicates that the cluster has an abnormal configuration.

**Scenario 1: Abnormal node pool configuration**

To fix this issue, perform the following operations:

#. Log in to the CCE console. In the navigation pane, choose **Nodes**.
#. Locate the affected node pool and choose **More** > **Manage**.
#. In **Networking Components**, set the **nic-max-above-warm-target** value to a maximum of 256.
#. Click **OK**.

**Scenario 2: Abnormal cluster configuration**

To fix this issue, perform the following operations:

#. Log in to the CCE console. In the navigation pane, choose **Settings**.
#. Click the **Network** tab and set the **Threshold for Unbinding Pre-bound Container ENIs** value to a maximum of 256.
#. Click **Confirm Configuration**.
