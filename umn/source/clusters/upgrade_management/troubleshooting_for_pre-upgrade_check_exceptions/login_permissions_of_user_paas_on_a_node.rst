:original_name: cce_10_0499.html

.. _cce_10_0499:

Login Permissions of User **paas** on a Node
============================================

Check Items
-----------

Check whether user **paas** is allowed to log in to a node.

Solutions
---------

Run the following command to check whether user **paas** is allowed to log in to a node:

.. code-block::

   sudo grep "paas" /etc/passwd

If the permissions assigned to user **paas** contain **nologin** or **false**, the user does not have the login permissions. In this case, restore the login permissions of user **paas**.

Run the following command to restore the login permissions of user **paas**:

.. code-block::

   usermod -s /bin/bash paas
