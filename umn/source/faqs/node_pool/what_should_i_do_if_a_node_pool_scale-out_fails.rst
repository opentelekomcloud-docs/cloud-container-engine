:original_name: cce_faq_00432.html

.. _cce_faq_00432:

What Should I Do If a Node Pool Scale-Out Fails?
================================================

Fault Locating
--------------

Locate the fault based on the events of the failure to scale out a node pool, as shown in :ref:`Table 1 <cce_faq_00432__table230510269532>`.

.. _cce_faq_00432__table230510269532:

.. table:: **Table 1** Node pool scale-out failure

   +----------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------+
   | Event                                                                                                                      | Possible Cause                                                                                                                                                                   | Reference                                                                                                      |
   +============================================================================================================================+==================================================================================================================================================================================+================================================================================================================+
   | ...call fsp to query keypair fail, error code : Ecs.0314, reason is : the keypair \**\* does not match the user_id \***... | The possible causes are as follows:                                                                                                                                              | :ref:`Failed to Obtain the Key Pair Used for Logging In to a Node Pool <cce_faq_00432__section14167104115340>` |
   |                                                                                                                            |                                                                                                                                                                                  |                                                                                                                |
   |                                                                                                                            | -  The key pair selected for logging in to the node pool has been deleted.                                                                                                       |                                                                                                                |
   |                                                                                                                            | -  The key pair selected for logging in to the node pool is a private one which cannot be used by the current user to log in to the node pool and create nodes in the node pool. |                                                                                                                |
   +----------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------+
   | {"error":{"message":"encrypted key id [``***``] is invalid.","code":"Ecs.0912"}}                                           | The possible causes are as follows:                                                                                                                                              | :ref:`Invalid KMS Key ID <cce_faq_00432__section7744554183>`                                                   |
   |                                                                                                                            |                                                                                                                                                                                  |                                                                                                                |
   |                                                                                                                            | -  The KMS key ID entered during node pool creation does not exist.                                                                                                              |                                                                                                                |
   |                                                                                                                            | -  The KMS key ID entered during node pool creation is the key of another user, but the user has not authorized you.                                                             |                                                                                                                |
   +----------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------+
   | Security group [``*****``] not found                                                                                       | This issue can arise in the following scenarios:                                                                                                                                 | :ref:`The Security Group Specified by the Node Pool Deleted <cce_faq_00432__section12755424185916>`            |
   |                                                                                                                            |                                                                                                                                                                                  |                                                                                                                |
   |                                                                                                                            | -  A custom security group is set up for the node pool but gets deleted, so the node pool scale-out fails.                                                                       |                                                                                                                |
   |                                                                                                                            | -  No custom security group is configured for the node pool and the default security group is deleted, so the node pool scale-out fails.                                         |                                                                                                                |
   +----------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------+

.. _cce_faq_00432__section14167104115340:

Failed to Obtain the Key Pair Used for Logging In to a Node Pool
----------------------------------------------------------------

If a node pool scale-out fails, the event contains **Ecs.0314**. This error code indicates that the key pair used for logging in to the node pool cannot be obtained, which results in the creation failure of a new ECS.

.. code-block::

   ...call fsp to query keypair fail, error code : Ecs.0314, reason is : the keypair *** does not match the user_id ***...

The possible causes are as follows:

-  The key pair selected for logging in to the node pool has been deleted.
-  The key pair selected for logging in to the node pool is a private one which cannot be used by the current user to log in to the node pool and create nodes in the node pool.

**Solution**:

-  If the scale-out fails due to the first cause, you can create a key pair and then create a node pool which can be logged in to using this key pair.
-  If the scale-out fails due to the second cause, only the user who created the private key pair can scale out the node pool. You can use another key pair when creating a new node pool.

.. _cce_faq_00432__section7744554183:

Invalid KMS Key ID
------------------

When a node pool fails to be expanded, the reported event contains **Ecs.0912**.

.. code-block::

   {"error":{"message":"encrypted key id [***] is invalid.","code":"Ecs.0912"}}

The possible causes are as follows:

-  The KMS key ID entered during node pool creation does not exist.
-  The KMS key ID entered during node pool creation is the key of another user, but the user has not authorized you.

**Solution**:

-  If the scale-out fails due to the first cause, ensure that the entered key ID exists.
-  If the scale-out fails due to second cause, use the ID of the shared key that has been authorized to you.

.. _cce_faq_00432__section12755424185916:

The Security Group Specified by the Node Pool Deleted
-----------------------------------------------------

When a node pool fails to be expanded, the event contains the following information:

.. code-block::

   Security group [*****] not found

This issue can arise in the following scenarios:

-  Scenarios 1: A custom security group is set up for the node pool but gets deleted, so the node pool scale-out fails.
-  Scenarios 2: No custom security group is configured for the node pool and the default security group is deleted, so the node pool scale-out fails.

**Solution**:

-  Scenario 1: Update the security group specified by the **customSecurityGroups** field by calling the API for updating a node pool.
-  Scenario 2: Log in to the CCE console and change the **default node security group** on the **Settings** page of the cluster. The new node security group must meet the communication rules of the cluster ports. For details, see :ref:`How Can I Configure a Security Group Rule for a Cluster? <cce_faq_00265>`
