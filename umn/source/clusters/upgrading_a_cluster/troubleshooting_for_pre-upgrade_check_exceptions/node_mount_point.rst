:original_name: cce_10_0459.html

.. _cce_10_0459:

Node Mount Point
================

Check Item
----------

Check whether inaccessible mount points exist on the node.

Solution
--------

**Scenario: There are inaccessible mount points on the node.**

If network NFS (such as OBS, SFS, and SFS) is used by the node and the node is disconnected with the NFS server, the mount point would be inaccessible and all processes that access this mount point are suspended.

#. Log in to the node.

#. Run the following commands on the node in sequence:

   .. code-block::

      - df -h
      - for dir in `df -h | grep -v "Mounted on" | awk "{print \\$NF}"`;do cd $dir; done && echo "ok"

#. If **ok** is returned, no problem occurs.

   Otherwise, start another terminal and run the following command to check whether the previous command is in the D state:

   .. code-block::

      - ps aux | grep "D "

#. If a process is in the D state, the problem occurs.You can only reset the node to solve the problem. Reset the node and upgrade the cluster again. For details about how to reset a node, see :ref:`Resetting a Node <cce_10_0003>`.

   .. note::

      Resetting a node will reset all node labels, which may affect workload scheduling. Before resetting a node, check and retain the labels that you have manually added to the node.
