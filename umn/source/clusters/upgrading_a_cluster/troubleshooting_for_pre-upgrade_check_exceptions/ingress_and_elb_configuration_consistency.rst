:original_name: cce_10_0526.html

.. _cce_10_0526:

Ingress and ELB Configuration Consistency
=========================================

Check Items
-----------

Check whether any modifications have been made on the ELB console to the listener, forwarding policy, forwarding rule, backend server group, backend cloud server, or certificate configurations that were automatically generated for the ingress.

Any modifications made to ELB will be overwritten during the upgrade process. Verify the configuration consistency before upgrading the cluster.

Solutions
---------

Based on the diagnostic analysis logs, determine which configurations require correction. Typically, this issue occurs when different forwarding policies are configured for the listener associated with the target ingress. This causes a mismatch with the forwarding policies configured for the cluster ingress. To resolve this, delete the additional forwarding policies from the current listener and add them on another listener.
