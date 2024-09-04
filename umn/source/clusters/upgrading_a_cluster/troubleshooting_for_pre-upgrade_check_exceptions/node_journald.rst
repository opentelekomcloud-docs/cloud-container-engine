:original_name: cce_10_0456.html

.. _cce_10_0456:

Node journald
=============

Check Items
-----------

Check whether journald of a node is normal.

Solution
--------

Log in to the node and run the **systemctl is-active systemd-journald** command to obtain the running status of journald. If the command output is abnormal, run the **systemctl restart systemd-journald** command and obtain the status again.

The normal command output is as follows:


.. figure:: /_static/images/en-us_image_0000001981436649.png
   :alt: **Figure 1** Running status of journald

   **Figure 1** Running status of journald

If the problem persists after journald is restarted, contact technical support.
