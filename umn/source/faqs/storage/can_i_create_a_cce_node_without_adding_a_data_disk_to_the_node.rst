:original_name: cce_faq_00089.html

.. _cce_faq_00089:

Can I Create a CCE Node Without Adding a Data Disk to the Node?
===============================================================

-  If **System Component Storage** is set to **System Disk**, you do not need to add a data disk.

-  Data disks are required if **System Component Storage** is set to **Data Disk**.

   A data disk dedicated for kubelet and the container engine will be attached to a new node. By default, CCE uses LVM to manage data disks. With LVM, you can adjust the disk space ratio for different resources on a data disk.

   If the data disk is uninstalled or damaged, the container engine will malfunction and the node becomes unavailable.
