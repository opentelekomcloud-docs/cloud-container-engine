:original_name: cce_10_0484.html

.. _cce_10_0484:

Health of Worker Node Components
================================

Check Items
-----------

Check whether the container runtime and network components on the worker nodes are healthy.

Solutions
---------

-  Scenario 1: CNI Agent is not active.

   If your cluster version is earlier than v1.17.17, or you are using the tunnel network model and your cluster version is later than v1.17.17, log in to the node and run the **systemctl status canal** command to check the status of canal. If you encounter an error, run the **systemctl restart canal** command and check the status again.

   If you are using the VPC or Cloud Native 2.0 network model and your cluster version is later than v1.17.17, log in to the node and run the **systemctl status yangtse** command to check the status of Yangtse. If you encounter an error, run the **systemctl restart yangtse** command and check the status again.

-  Scenario 2: kube-proxy is not active.

   Log in to the node and run the **systemctl is-active kube-proxy** command to check the status of kube-proxy. If you encounter an error, run the **systemctl restart kube-proxy** command and check the status again.

   If the fault persists, reset the node. For details, see :ref:`Resetting a Node <cce_10_0003>`.
