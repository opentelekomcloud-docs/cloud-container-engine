:original_name: cce_faq_00286.html

.. _cce_faq_00286:

What Should I Do If a Node Fails to Be Accepted Because It Fails to Be Installed?
=================================================================================

Symptom
-------

A node fails to be accepted into a cluster.

Possible Causes
---------------

Log in to the node and check the **/var/paas/sys/log/baseagent/baseagent.log** installation log. The following error information is displayed:

|image1|

Check the LVM settings of the node. It is found that the LVM logical volume is not created in **/dev/vdb**.

Solution
--------

Run the following command to manually create a logical volume:

.. code-block::

   pvcreate /dev/vdb
   vgcreate vgpaas /dev/vdb

After the node is reset on the GUI, the node becomes normal.

.. |image1| image:: /_static/images/en-us_image_0000001981275177.jpg
