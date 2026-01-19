:original_name: cce_10_0410.html

.. _cce_10_0410:

Changing the RuntimeMaxUse of the Memory Used by the Log Cache on a Node
========================================================================

Journald is a log system in Linux. It writes log information into binary files and uses **/run/log/journal** as the log cache directory by default. The Journald configuration file is stored in the **/etc/systemd/journald.conf** directory on the node. The **RuntimeMaxUse** parameter indicates the maximum memory usage of the log cache. If **RuntimeMaxUse** is not set, a large amount of memory will be occupied after the system runs for a long time.

.. important::

   The commands for modifying node system parameters are valid only when public images are used. The commands provided in this document are for reference only when private images are used.

.. _cce_10_0410__section103357276311:

Changing RuntimeMaxUse
----------------------

#. Log in to the node and view the **/etc/systemd/journald.conf** file.

   .. code-block::

      cat /etc/systemd/journald.conf

#. Modify **RuntimeMaxUse**. The recommended value is **100M**.

   -  If **RuntimeMaxUse** has been set in the **journald.conf** file, run the following command to change the value:

      .. code-block::

         sed -i "s/RuntimeMaxUse=[0-9]*M/RuntimeMaxUse=100M/g" /etc/systemd/journald.conf && systemctl restart systemd-journald

   -  If **RuntimeMaxUse** is not set in the **journald.conf** file, run the following command to add it:

      .. code-block::

         echo RuntimeMaxUse=100M >> /etc/systemd/journald.conf && systemctl restart systemd-journald

#. Verify the modification. If the returned value is the same as the modified value, the modification is correct.

   .. code-block::

      cat /etc/systemd/journald.conf | grep RuntimeMaxUse

Automatically Configuring RuntimeMaxUse When Creating a Node or Node Pool
-------------------------------------------------------------------------

You can set the script to be executed after a node or node pool is created. When creating a node or node pool, you can use the script to configure the **RuntimeMaxUse** size.

#. Confirm the OS of the node or node pool to be created.

#. Manually test the script commands on nodes **in the same cluster and running the same OS**. For details about how to manually run the script, see :ref:`Changing RuntimeMaxUse <cce_10_0410__section103357276311>`.

#. When creating a node or node pool, choose **Advanced Settings** > **Post-installation Command** to add commands. (The following commands must be configured after the verification is successful.)

   -  Log in to the node and view the **/etc/systemd/journald.conf** file. If **RuntimeMaxUse** has been set, run the following command to change the value:

      .. code-block::

         sed -i "s/RuntimeMaxUse=[0-9]*M/RuntimeMaxUse=100M/g" /etc/systemd/journald.conf && systemctl restart systemd-journald

   -  Log in to the node and view the **/etc/systemd/journald.conf** file. If **RuntimeMaxUse** is not set, run the following command to add it:

      .. code-block::

         echo RuntimeMaxUse=100M >> /etc/systemd/journald.conf && systemctl restart systemd-journald

#. After the node is created, log in to the node to check whether the parameter is successfully modified.

   .. code-block::

      cat /etc/systemd/journald.conf | grep RuntimeMaxUse
