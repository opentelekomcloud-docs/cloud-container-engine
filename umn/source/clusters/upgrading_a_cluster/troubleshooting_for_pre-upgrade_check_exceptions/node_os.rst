:original_name: cce_10_0451.html

.. _cce_10_0451:

Node OS
=======

Check Items
-----------

Check whether the OS kernel version of the node is supported by CCE.

Solution
--------

-  **Case 1: The node image is not a standard CCE image.**

   CCE nodes run depending on the initial standard kernel version specified when they are created. CCE has performed comprehensive compatibility tests based on this kernel version. A non-standard kernel version may lead to unexpected compatibility issues during a node upgrade and the upgrade may fail. For details, see :ref:`High-Risk Operations and Solutions <cce_10_0054>`.

   Do not directly upgrade this type of nodes. Instead, :ref:`reset the nodes <cce_10_0003>` to a standard kernel version and then upgrade the nodes.

-  **Case 2: An image of a special version is defective.**

   A EulerOS release 2.8 (Arm) image of v1.17 is used in the source version. Such an image is defective because the **docker exec** command will be affected after Docker is restarted. When the cluster version is upgraded, the Docker version will be updated and Docker will be restarted. To resolve this issue, do as follows:

   #. Empty and isolate the affected nodes before upgrading the cluster.
   #. Upgrade the version to v1.19 or later and reset the nodes to replace the image with one of a later version, for example, EulerOS release 2.9.
