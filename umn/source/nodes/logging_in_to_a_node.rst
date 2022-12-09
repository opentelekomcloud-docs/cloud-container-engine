:original_name: cce_01_0185.html

.. _cce_01_0185:

Logging In to a Node
====================

Notes and Constraints
---------------------

-  If you use SSH to log in to a node (an ECS), ensure that the ECS already has an EIP (a public IP address).
-  Only login to a running ECS is allowed.
-  Only the user linux can log in to a Linux server.

Login Modes
-----------

You can log in to an ECS in either of the following modes:

-  Management console (VNC)

   If an ECS has no EIP, log in to the ECS console and click **Remote Login** in the same row as the ECS.

-  SSH

   This mode applies only to ECSs running Linux. Usually, you can use a remote login tool, such as PuTTY, Xshell, and SecureCRT, to log in to your ECS. If none of the remote login tools can be used, log in to the ECS console and click **Remote Login** in the same row as the ECS to view the connection status and running status of the ECS.

   .. note::

      -  When you use the Windows OS to log in to a Linux node, set **Auto-login username** to linux.
      -  The CCE console does not support node OS upgrade. Do not upgrade the node OS using the **yum update** command. Otherwise, the container networking components will be unavailable.

.. table:: **Table 1** Linux ECS login modes

   +-------------+----------------+---------------------------------------------------------+
   | EIP Binding | On-Premises OS | Connection Method                                       |
   +=============+================+=========================================================+
   | Yes         | Windows        | Use a remote login tool, such as PuTTY or Xshell.       |
   +-------------+----------------+---------------------------------------------------------+
   | Yes         | Linux          | Run commands.                                           |
   +-------------+----------------+---------------------------------------------------------+
   | Yes/No      | Windows/Linux  | Use the remote login function available on the console. |
   +-------------+----------------+---------------------------------------------------------+
