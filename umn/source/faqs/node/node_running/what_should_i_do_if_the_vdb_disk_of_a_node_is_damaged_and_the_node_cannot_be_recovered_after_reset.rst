:original_name: cce_faq_00263.html

.. _cce_faq_00263:

What Should I Do If the vdb Disk of a Node Is Damaged and the Node Cannot Be Recovered After Reset?
===================================================================================================

Symptom
-------

The vdb disk of a node is damaged and the node cannot be recovered after reset.

**Error Scenarios**

-  On a normal node, delete the LV and VG. The node is unavailable.

-  Reset an abnormal node, and a syntax error is reported. The node is unavailable.

   The following figure shows the details.

   |image1|

Fault Locating
--------------

If the volume group (VG) on the node is deleted or damaged and cannot be identified, you need to manually restore the VG first to prevent your data disks from being formatted by mistake during the reset.

Solution
--------

#. Log in to the node.

#. Create a PV and a VG again. In this example, the following error message is displayed:

   .. code-block::

      root@host1:~# pvcreate /dev/vdb
      Device /dev/vdb excluded by a filter

   This is because the added disk is created on another VM and has a partition table. The current VM cannot identify the partition table of the disk. You need to run the **parted** commands for three times to re-create the partition table.

   .. code-block::

      root@host1:~# parted /dev/vdb
      GNU Parted 3.2
      Using /dev/vdb
      Welcome to GNU Parted! Type 'help' to view a list of commands.
      (parted) mklabel msdos
      Warning: The existing disk label on /dev/vdb will be destroyed and all data on this disk will be lost. Do you want to continue?
      Yes/No? yes
      (parted) quit
      Information: You may need to update /etc/fstab.

   Run **pvcreate** again. When the system asks you whether to erase the DOS signature, enter **y**. The disk is created as a PV.

   .. code-block::

      root@host1:~# pvcreate /dev/vdb
      WARNING: dos signature detected on /dev/vdb at offset 510. Wipe it? [y/n]: y
      Wiping dos signature on /dev/vdb.
      Physical volume "/dev/vdb" successfully created

#. Create a VG.

   Check the Docker disks of the node. If the disks are **/dev/vdb** and **/dev/vdc**, run the following command:

   .. code-block::

      root@host1:~# vgcreate vgpaas /dev/vdb /dev/vdc

   If there is only the **/dev/vdb** disk, run the following command:

   .. code-block::

      root@host1:~# vgcreate vgpaas /dev/vdb

   After the creation is complete, reset the node.

.. |image1| image:: /_static/images/en-us_image_0000001851585280.png
