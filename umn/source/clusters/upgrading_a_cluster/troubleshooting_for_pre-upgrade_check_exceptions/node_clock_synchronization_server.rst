:original_name: cce_10_0450.html

.. _cce_10_0450:

Node Clock Synchronization Server
=================================

Check Items
-----------

Check whether the clock synchronization server ntpd or chronyd of the node is running properly.

Solution
--------

-  **Scenario 1: ntpd is running abnormally.**

   Log in to the node and run the **systemctl status ntpd** command to obtain the running status of ntpd. If the command output is abnormal, run the **systemctl restart ntpd** command and obtain the status again.

   The normal command output is as follows:


   .. figure:: /_static/images/en-us_image_0000001897906253.png
      :alt: **Figure 1** Running status of ntpd

      **Figure 1** Running status of ntpd

   If the problem persists after ntpd is restarted, contact technical support.

-  **Scenario 2: chronyd is running abnormally.**

   Log in to the node and run the **systemctl status chronyd** command to obtain the running status of chronyd. If the command output is abnormal, run the **systemctl restart chronyd** command and obtain the status again.

   The normal command output is as follows:


   .. figure:: /_static/images/en-us_image_0000001898025781.png
      :alt: **Figure 2** Running status of chronyd

      **Figure 2** Running status of chronyd

   If the problem persists after chronyd is restarted, contact technical support.
