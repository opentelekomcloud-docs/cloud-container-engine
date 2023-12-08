:original_name: cce_faq_00127.html

.. _cce_faq_00127:

What Should I Do If No Node Creation Record Is Displayed When the Node Pool Is Being Expanding?
===============================================================================================

Symptom
-------

The node pool keeps being in the expanding state, but no node creation record is displayed in the operation record.

Troubleshooting
---------------

Check and rectify the following faults:

-  Whether the specifications configured for the node pool are insufficient.
-  Whether the ECS or memory quota of the tenant is insufficient.
-  The ECS capacity verification of the tenant may fail if too many nodes are created at a time.

Solution
--------

-  If the resources of the ECS flavor cannot meet service requirements, use ECSs of another flavor.
-  If the ECS or memory quota is insufficient, increase the quota.
-  If the ECS capacity verification fails, perform the verification again.
