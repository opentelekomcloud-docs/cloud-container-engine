:original_name: cce_10_0442.html

.. _cce_10_0442:

CCE Agent Versions
==================

Check Items
-----------

Check whether cce-agent on the current node is of the latest version.

Solution
--------

-  **Scenario 1: The error message "you cce-agent no update, please restart it" is displayed.**

   cce-agent does not need to be updated but is not restarted. In this case, log in to the node and manually restart cce-agent.

   Solution: Log in to the node and run the following command:

   .. code-block::

      systemctl restart cce-agent

   Perform the pre-upgrade check again.

-  **Scenario 2: The error message "your cce-agent is not the latest version" is displayed.**

   cce-agent is not of the latest version, and the automatic update failed. This issue is typically caused by an invalid OBS path or the component version is outdated.

   Solution

   #. Log in to a node where the check succeeded, obtain the path of the cce-agent configuration file, and obtain the OBS address.

      .. code-block::

         cat `ps aux | grep cce-agent | grep -v grep | awk -F '-f ' '{print $2}'`

      The OBS configuration address field in the configuration file is **packageFrom.addr**.


      .. figure:: /_static/images/en-us_image_0000001897906125.png
         :alt: **Figure 1** OBS address

         **Figure 1** OBS address

   #. Log in to a where the check failed, obtain the OBS address again by referring to the previous step, and check whether the OBS addresses are the same. If they are different, change the OBS address of the abnormal node to the correct address.

   #. Run the following commands to download the latest binary file:

      -  x86

         .. code-block::

            curl -k "https://{OBS address you have obtained}/cluster-versions/base/cce-agent" > /tmp/cce-agent

      -  Arm

         .. code-block::

            curl -k "https://{OBS address you have obtained}/cluster-versions/base/cce-agent-arm" > /tmp/cce-agent-arm

   #. Replace the original cce-agent binary file.

      -  x86

         .. code-block::

            mv -f /tmp/cce-agent /usr/local/bin/cce-agent
            chmod 750 /usr/local/bin/cce-agent
            chown root:root /usr/local/bin/cce-agent

      -  Arm

         .. code-block::

            mv -f /tmp/cce-agent-arm /usr/local/bin/cce-agent-arm
            chmod 750 /usr/local/bin/cce-agent-arm
            chown root:root /usr/local/bin/cce-agent-arm

   #. Restart cce-agent.

      .. code-block::

         systemctl restart cce-agent

      If you have any questions about the preceding operations, contact technical support.
