:original_name: cce_10_0442.html

.. _cce_10_0442:

Node CCEAgent Version
=====================

Check Item
----------

Check whether cce-agent on the current node is of the latest version.

Solution
--------

If cce-agent is not of the latest version, the automatic update fails. This problem is usually caused by invalid OBS address or the version of the component is outdated.

#. Log in to a normal node that passes the check, obtain the path of the cce-agent configuration file, and check the OBS address.

   .. code-block::

      cat `ps aux | grep cce-agent | grep -v grep | awk -F '-f ''{print $2}'`

   The OBS configuration address field in the configuration file is **packageFrom.addr**.

   |image1|

#. Log in to an abnormal node where the check fails, obtain the OBS address again by referring to the previous step, and check whether the OBS address is consistent. If they are different, change the OBS address of the abnormal node to the correct address.

#. Run the following commands to download the latest binary file:

   -  ARM

      .. code-block::

         curl -k "https://{OBS address you have obtained}/cluster-versions/base/cce-agent-arm" > /tmp/cce-agent-arm

#. Replace the original cce-agent binary file.

   -  ARM

      .. code-block::

         mv -f /tmp/cce-agent-arm /usr/local/bin/cce-agent-arm
         chmod 750 /usr/local/bin/cce-agent-arm
         chown root:root /usr/local/bin/cce-agent-arm

#. Restart cce-agent.

   .. code-block::

      systemctl restart cce-agent

   If you have any questions about the preceding operations, contact technical support.

.. |image1| image:: /_static/images/en-us_image_0000001629186693.png
