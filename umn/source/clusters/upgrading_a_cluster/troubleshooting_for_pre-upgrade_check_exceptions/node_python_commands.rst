:original_name: cce_10_0453.html

.. _cce_10_0453:

Node Python Commands
====================

Check Items
-----------

Check whether the Python commands are available on a node.

Check Method
------------

.. code-block::

   /usr/bin/python --version
   echo $?

If the command output is not 0, the check fails.

Solution
--------

Install Python before the upgrade.
