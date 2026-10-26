:original_name: cce_bestpractice_0353.html

.. _cce_bestpractice_0353:

Adjusting the DNS Settings of the VPC and VM
============================================

When the CoreDNS add-on is started, it obtains the DNS settings from the **resolve.conf** file from the deployed instance by default and uses the obtained information as the upstream resolution server address. Before CoreDNS restarts, **resolve.conf** on the node is not reloaded. Here are some suggestions:

-  Ensure that the **resolve.conf** file of each node in the cluster is the same. In this way, CoreDNS can schedule requests to any node in the cluster.
-  When modifying the **resolve.conf** file of a node, if the node has a CoreDNS pod, restart CoreDNS timely to ensure status consistency.
