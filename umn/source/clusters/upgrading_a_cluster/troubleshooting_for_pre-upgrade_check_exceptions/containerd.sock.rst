:original_name: cce_10_0457.html

.. _cce_10_0457:

containerd.sock
===============

Check Items
-----------

Check whether the **containerd.sock** file is on the node. This file affects the startup of container runtime in the Euler OS.

Solution
--------

**Scenario: The Docker used by the node is the customized Euler-docker.**

#. Log in to the node.
#. Run the **rpm -qa \| grep docker \| grep euleros** command. If the command output is not empty, the Docker used on the node is Euler-docker.
#. Run the **stat /run/containerd/containerd.sock** command. If the file exists, Docker startup will fail.
#. Run the **rm -rf /run/containerd/containerd.sock** command and perform the cluster upgrade check again.
