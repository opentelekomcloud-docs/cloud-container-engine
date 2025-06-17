:original_name: cce_10_0459.html

.. _cce_10_0459:

Node Mount Points
=================

Check Items
-----------

Check whether there are inaccessible mount points on the node.

Solution
--------

**Scenario: There are inaccessible mount points on the node.**

If NFS (such as obsfs or SFS) is used by the node and the node is disconnected from the NFS server, the mount point would be inaccessible and all processes that access this mount point are in D state.

#. Log in to the node.

#. Create a script file, for example, **/tmp/check_hang_mount.sh** on the node. The content of the script file is as follows:

   .. code-block::

      for mount_path in `cat /proc/self/mountinfo | awk '{print $5}' | grep -v netns`
      do
          timeout 10 sh -c "cd $mount_path"
          if [ $? == 124 ];then
              echo "$mount_path hang mount"
          fi
      done

#. Run the saved script and check the output.

   |image1|

   The mount points of the **/root/foo** and **/root/bar** folders are incorrect.

#. Run the following command to check the suspended mount points:

   .. code-block::

      mount -n | grep /root/foo

   |image2|

   When a mount point is suspended, it typically indicates that services are not using it anymore. After confirming that the mount point is no longer required, run the following command to unmount it and then re-execute the previously mentioned script:

   .. code-block::

      umount -l -f localhost:/tmp/nfs

   |image3|

   After the execution, perform the check again on the pre-upgrade check page.

.. |image1| image:: /_static/images/en-us_image_0000002253619909.png
.. |image2| image:: /_static/images/en-us_image_0000002253779825.png
.. |image3| image:: /_static/images/en-us_image_0000002218820018.png
