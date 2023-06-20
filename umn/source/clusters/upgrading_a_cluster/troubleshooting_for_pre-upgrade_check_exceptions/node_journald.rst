:original_name: cce_10_0456.html

.. _cce_10_0456:

Node journald
=============

Check Item
----------

Check whether journald of a node is normal.

Solution
--------

Log in to the node and run the **systemctl is-active systemd-journald** command to query the running status of journald. If the command output is abnormal, run the **systemctl restart systemd-journald** command and query the status again.

The normal command output is as follows:

|image1|

If the problem persists after journald is restarted, contact technical support.

.. |image1| image:: /_static/images/en-us_image_0000001517903128.png
