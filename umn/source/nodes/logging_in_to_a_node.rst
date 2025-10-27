:original_name: cce_10_0185.html

.. _cce_10_0185:

Logging In to a Node
====================

Prerequisites
-------------

-  Before you log in to a node using SSH, ensure the SSH port (22 by default) is enabled in the security group of the node.
-  Before you log in to a node (an ECS) via SSH over the Internet, ensure the ECS already has an EIP bound.
-  Only a running ECS can be logged in to.
-  Only user **linux** can log in to a Linux server.

Login Modes
-----------

You can log in to an ECS in either of the following modes:

-  Management console (VNC)

   If an ECS has no EIP, log in to the ECS console and click **Remote Login** in the same row as the ECS.

   For details, see `Login Using VNC <https://docs.otc.t-systems.com/en-us/usermanual/ecs/en-us_topic_0093263550.html>`__.

-  SSH

   This mode applies only to ECSs running Linux. You can typically use a remote login tool like PuTTY, Xshell, or SecureCRT to log in to your ECS. If none of the remote login tools can be used, log in to the ECS console and click **Remote Login** in the same row as the ECS to view the connection status and running status of the ECS.

   .. note::

      -  When you log in to a Linux node from Windows, set **Auto-login username** to **linux**.
      -  The CCE console does not support node OS upgrade. Do not upgrade the node OS using the **yum update** command. Otherwise, the container networking components will be unavailable.

.. table:: **Table 1** Linux ECS login modes

   +-----------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | EIP Binding           | On-Premises OS        | Connection Method                                                                                                                                              |
   +=======================+=======================+================================================================================================================================================================+
   | Yes                   | Windows               | Use a remote login tool, such as PuTTY or Xshell.                                                                                                              |
   |                       |                       |                                                                                                                                                                |
   |                       |                       | -  SSH key authentication: `Logging In to a Linux ECS Using an SSH Key Pair <https://docs.otc.t-systems.com/usermanual/ecs/en-us_topic_0017955380.html>`__     |
   +-----------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Yes                   | Linux                 | Run commands.                                                                                                                                                  |
   |                       |                       |                                                                                                                                                                |
   |                       |                       | -  SSH key authentication: `Logging In to a Linux ECS Using an SSH Key Pair <https://docs.otc.t-systems.com/usermanual/ecs/en-us_topic_0017955380.html>`__     |
   +-----------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Yes/No                | Windows/Linux         | Remote login using the management console: `Logging In to a Linux ECS Using VNC <https://docs.otc.t-systems.com/usermanual/ecs/en-us_topic_0093263550.html>`__ |
   +-----------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------+
