:original_name: cce_10_0527.html

.. _cce_10_0527:

Network Policies of Cluster Network Components
==============================================

Check Items
-----------

Check the network policy settings on the master nodes in your cluster. If any manual modifications have been made, they will be reset during the upgrade.

Solutions
---------

Check whether network policies need to be disabled on the network component canal-controller based on the diagnostic analysis logs. For example, if a cluster uses a Direct Connect connection to access an external address, the external switch does not support **ip-option**. Enabling network policies in this scenario could result in network access failure. In such cases, manually disable the network policies.

If you need to modify the network policy setting, skip this check. After the upgrade, you can modify the network policy setting on the **Network** tab in **Settings**.
