:original_name: cce_faq_00432.html

.. _cce_faq_00432:

What Should I Do If a Node Pool Scale-Out Fails?
================================================

Fault Locating
--------------

Locate the fault based on the event of the failure to scale out a node pool, as shown in :ref:`Table 1 <cce_faq_00432__table230510269532>`.

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
