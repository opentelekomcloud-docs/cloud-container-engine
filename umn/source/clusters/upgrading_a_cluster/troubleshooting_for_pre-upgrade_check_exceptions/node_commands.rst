:original_name: cce_10_0506.html

.. _cce_10_0506:

Node Commands
=============

Check Items
-----------

Check whether the commands required for the upgrade are available on the node.

Solution
--------

The cluster upgrade failure is typically caused by the lack of key node commands that are required in the cluster upgrade.

Error messages:

.. code-block::

   __error_code#ErrorCommandNotExist#chage command is not exists#__
   __error_code#ErrorCommandNotExist#chown command is not exists#__
   __error_code#ErrorCommandNotExist#chmod command is not exists#__
   __error_code#ErrorCommandNotExist#mkdir command is not exists#__
   __error_code#ErrorCommandNotExist#in command is not exists#__
   __error_code#ErrorCommandNotExist#touch command is not exists#__
   __error_code#ErrorCommandNotExist#pidof command is not exists#__

The preceding error messages indicate the lack of node commands such as **chage**, **chown**, and **chmod**. Add these commands and check the node commands again.
