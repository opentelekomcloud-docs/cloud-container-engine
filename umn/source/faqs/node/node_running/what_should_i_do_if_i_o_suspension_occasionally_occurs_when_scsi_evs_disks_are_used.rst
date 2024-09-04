:original_name: cce_faq_00296.html

.. _cce_faq_00296:

What Should I Do If I/O Suspension Occasionally Occurs When SCSI EVS Disks Are Used?
====================================================================================

Symptom
-------

When SCSI EVS disks are used and containers are created and deleted on a CentOS node, the disks are frequently mounted and unmounted. The read/write rate of the system disk may instantaneously surge. As a result, the system is suspended, affecting the normal node running.

When this problem occurs, the following information is displayed in the dmesg log:

.. code-block::

   Attached SCSI disk
   task jdb2/xxx blocked for more than 120 seconds.

Example:

|image1|

Possible Causes
---------------

After a PCI device is hot added to BUS 0, the Linux OS kernel will traverse all the PCI bridges mounted to BUS 0 for multiple times, and these PCI bridges cannot work properly during this period. During this period, if the PCI bridge used by the device is updated, due to a kernel defect, the device considers that the PCI bridge is abnormal, and the device enters a fault mode and cannot work normally. If the front end is writing data into the PCI configuration space for the back end to process disk I/Os, the write operation may be deleted. As a result, the back end cannot receive notifications to process new requests on the I/O ring. Finally, the front-end I/O suspension occurs.

Impact
------

CentOS Linux kernels of versions earlier than 3.10.0-1127.el7 are affected.

Solution
--------

Upgrade the kernel to a later version **by resetting the node**.

.. |image1| image:: /_static/images/en-us_image_0000001950315912.png
