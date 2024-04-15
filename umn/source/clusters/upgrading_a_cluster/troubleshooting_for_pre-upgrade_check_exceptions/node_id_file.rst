:original_name: cce_10_0490.html

.. _cce_10_0490:

Node ID File
============

Check Items
-----------

Check the ID file format.

Solution
--------

#. On the **Nodes** page of the CCE console, click the name of the abnormal node to go to the ECS page.

#. Copy the node ID and save it to the local host.

#. Log in to the abnormal node and back up files.

   .. code-block::

      cp /var/lib/cloud/data/instance-id /tmp/instance-id
      cp /var/paas/conf/server.conf /tmp/server.conf

#. Log in to the abnormal node and write the obtained node ID to the file.

   .. code-block::

      echo "Node ID" > /var/lib/cloud/data/instance-id
      echo "Node ID" > /var/paas/conf/server.conf
