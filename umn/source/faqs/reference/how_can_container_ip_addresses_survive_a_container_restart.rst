:original_name: cce_faq_00192.html

.. _cce_faq_00192:

How Can Container IP Addresses Survive a Container Restart?
===========================================================

If Containers Will Run in a Single-Node Cluster
-----------------------------------------------

Add **hostNetwork: true** to the **spec.spec.** in the YAML file of the workload to which the containers will belong.

If Containers Will Run in a Multi-Node Cluster
----------------------------------------------

Configure node affinity policies, in addition to perform the operations described in "If the Container Runs in a Single-Node Cluster". However, after the workload is created, the number of running pods cannot exceed the number of affinity nodes.

Expected Result
---------------

After the previous settings are complete and the workload is running, the IP addresses of the workload's pods are the same as the node IP addresses. After the workload is restarted, these IP addresses will keep unchanged.
