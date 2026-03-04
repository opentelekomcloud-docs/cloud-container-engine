:original_name: cce_bestpractice_10020_0.html

.. _cce_bestpractice_10020_0:

Executing the Pre- or Post-installation Commands During Node Creation
=====================================================================

Background
----------

When creating a node, use the pre- or -installation commands to install tools or perform security hardening on the node. This section provides guidance for you to correctly use the pre- or post-installation scripts.

Precautions
-----------

-  Do not use pre- or post-installation scripts that take a long time to execute.

   The pre-installation script has a 15-minute time limit, while the post-installation script has a 30-minute time limit. If the node is not available within the designated time, the node reclaim process will be initiated. Therefore, do not use pre- or post-installation scripts that take a long time to execute.

-  Do not directly use **reboot** in the script.

   CCE executes the post-installation command after installing mandatory components on a node. The node will be available only after the post-installation command is executed. If you run **reboot** directly, the node may be restarted before its status is reported. As a result, it cannot reach the running state within 30 minutes, and a rollback due to timeout will be triggered. Therefore, do not use **reboot**.

   If you need to restart a node, perform the following operations:

   -  Run **shutdown -r** *<time >* in the script to delay the restart. For example, you can run **shutdown -r 1** to delay the restart for 1 minute.
   -  After the node is available, manually restart it.

Procedure
---------

#. Log in to the CCE console. In the navigation pane, choose **Clusters**. Click the target cluster name to access the cluster console.

#. Choose **Nodes** in the navigation pane, click the **Nodes** tab, click **Create Node** in the right corner, and configure the parameters.

#. In the **Advanced Settings** area, enter pre- or post-installation commands.

   |image1|

   For example, you can create iptables rules by running a post-installation command to allow a maximum of 25 TCP data packets to be addressed to port 80 per minute and allow a maximum of 100 data packets to be addressed to the port when the limit is exceeded to prevent DDoS attacks.

   .. code-block::

      iptables -A INPUT -p tcp --dport 80 -m limit --limit 25/minute --limit-burst 100 -j ACCEPT

   .. note::

      The command example here is for reference only.

#. After the configuration, enter the number of nodes to be created and click **Next: Confirm**.

#. Click **Submit**.

.. |image1| image:: /_static/images/en-us_image_0000002484118386.png
