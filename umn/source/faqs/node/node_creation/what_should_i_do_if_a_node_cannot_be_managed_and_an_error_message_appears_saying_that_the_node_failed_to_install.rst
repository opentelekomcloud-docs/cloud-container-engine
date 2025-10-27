:original_name: cce_faq_00286.html

.. _cce_faq_00286:

What Should I Do If a Node Cannot Be Managed and an Error Message Appears Saying That the Node Failed to Install?
=================================================================================================================

Symptom
-------

A node fails to be accepted into a cluster.

Possible Cause
--------------

Log in to the node and check the **/var/paas/sys/log/baseagent/baseagent.log** installation log. The following error information is displayed:

|image1|

Check the Logical Volume Manager (LVM) settings of the node. It is found that the LVM logical volume is not created in **/dev/vdb**.

Solution
--------

Run the following command to manually create a logical volume:

.. code-block::

   pvcreate /dev/vdb
   vgcreate vgpaas /dev/vdb

After the node is reset on the GUI, the node becomes normal.

.. |image1| image:: /_static/images/en-us_image_0000002434078176.jpg
