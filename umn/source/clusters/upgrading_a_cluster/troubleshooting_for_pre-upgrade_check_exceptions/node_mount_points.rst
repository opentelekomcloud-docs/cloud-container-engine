:original_name: cce_10_0459.html

.. _cce_10_0459:

Node Mount Points
=================

Check Items
-----------

Check whether inaccessible mount points exist on the node.

Solution
--------

**Scenario: There are inaccessible mount points on the node.**

If NFS (such as obsfs or SFS) is used by the node and the node is disconnected from the NFS server, the mount point would be inaccessible and all processes that access this mount point are in D state.

#. Log in to the node.

#. Run the following commands on the node in sequence:

   .. code-block::

      - df -h
      - for dir in `df -h | grep -v "Mounted on" | awk "{print \\$NF}"`;do cd $dir; done && echo "ok"

#. If **ok** is returned, no problem occurs.

   Otherwise, start another terminal and run the following command to check whether the previous command is in the D state:

   .. code-block::

      - ps aux | grep "D "

#. If a process is in the D state, the problem occurs. You can restart the node to solve the problem. Restart the node and upgrade the cluster again.

   .. note::

      Workloads running on the node will be rescheduled after a node is restarted. Check whether services will be affected before restarting the node.
