:original_name: cce_bestpractice_10020.html

.. _cce_bestpractice_10020:

Executing the Post-installation Command During Node Creation
============================================================

Background
----------

When creating a node, use the post-installation commands to install tools or perform security hardening on the node. This section provides guidance for you to correctly use the post-installation scripts.

Precautions
-----------

-  Do not use the post-installation script that takes a long time to execute.

   The time limit to create a node in the CCE clusters is 30 minutes. If the node is not available within 30 minutes, it will be reclaimed. Therefore, do not run the post-installation script that takes a long time.

-  Do not directly use the **reboot** command in the script.

   CCE executes the post-installation commands after installing mandatory components on the node. The node will be available only after the post-installation commands are executed. If you run **reboot** directly, the node may be restarted before its status is reported. As a result, it cannot reach the running state within 30 minutes, and a rollback due to timeout will be triggered. Therefore, do not run the **reboot** command.

   If you need to restart the node, perform the following operations:

   -  Run the **shutdown -r** *<time >* command in the script to delay the restart. For example, you can run **shutdown -r 1** to delay the restart for 1 minute.
   -  After the node is available, manually restart it.

Procedure
---------

#. Log in to the CCE console. In the navigation pane, choose **Clusters**. Click the target cluster name to access the cluster console.

#. Choose **Nodes** in the navigation pane, click the **Nodes** tab, click **Create Node** in the right corner, and configure the parameters.

#. In the **Advanced Settings** area, enter the post-installation command.

   |image1|

   For example, you can create iptables rules by running a post-installation command to allow a maximum of 25 TCP data packets to be addressed to port 80 per minute and allow a maximum of 100 data packets to be addressed to the port when the limit is exceeded to prevent DDoS attacks.

   .. code-block::

      iptables -A INPUT -p tcp --dport 80 -m limit --limit 25/minute --limit-burst 100 -j ACCEPT

   .. note::

      The command example here is for reference only.

#. After the configuration, enter the number of nodes to be created and click **Next: Confirm**.

#. Click **Submit**.

.. |image1| image:: /_static/images/en-us_image_0000001851744500.png
