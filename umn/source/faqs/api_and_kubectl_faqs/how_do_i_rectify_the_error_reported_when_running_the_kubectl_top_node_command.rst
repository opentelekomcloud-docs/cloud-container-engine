:original_name: cce_faq_00321.html

.. _cce_faq_00321:

How Do I Rectify the Error Reported When Running the kubectl top node Command?
==============================================================================

Symptom
-------

The error message "Error from server (ServiceUnavailable): the server is currently unable to handle the request (get nodes.metrics.k8s.io)" is displayed after the **kubectl top node** command is executed.

Possible Causes
---------------

"Error from server (ServiceUnavailable)" indicates that the cluster is not connected. In this case, you need to check whether the network between kubectl and the master node in the cluster is normal.

Solution
--------

-  If the kubectl command is executed outside the cluster, check whether the cluster is bound to an EIP. If yes, download the **kubeconfig** file and run the kubectl command again.
-  If the kubectl command is executed on a node in the cluster, check the security group of the node and check whether the TCP/UDP communication between the worker node and master node is allowed. For details about the security group, see :ref:`How Can I Configure a Security Group Rule in a Cluster? <cce_faq_00265>`.
