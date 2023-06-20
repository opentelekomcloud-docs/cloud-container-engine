:original_name: cce_10_0450.html

.. _cce_10_0450:

Node Clock Synchronization Server
=================================

Check Item
----------

Check whether the clock synchronization server ntpd or chronyd of the node is running properly.

Solution
--------

-  **Scenario 1: ntpd is running abnormally.**

   Log in to the node and run the **systemctl status ntpd** command to query the running status of ntpd. If the command output is abnormal, run the **systemctl restart ntpd** command and query the status again.

   The normal command output is as follows:

   |image1|

   If the problem persists after ntpd is restarted, contact technical support.

-  **Scenario 2: chronyd is running abnormally.**

   Log in to the node and run the **systemctl status chronyd** command to query the running status of chronyd. If the command output is abnormal, run the **systemctl restart chronyd** command and query the status again.

   The normal command output is as follows:

   |image2|

   If the problem persists after chronyd is restarted, contact technical support.

.. |image1| image:: /_static/images/en-us_image_0000001568902509.png
.. |image2| image:: /_static/images/en-us_image_0000001518062624.png
