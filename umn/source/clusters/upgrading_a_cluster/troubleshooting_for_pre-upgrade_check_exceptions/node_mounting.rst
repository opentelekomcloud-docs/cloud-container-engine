:original_name: cce_10_0498.html

.. _cce_10_0498:

Node Mounting
=============

Check Items
-----------

Check whether the default mount directory and soft link on the node have been manually mounted or modified.

-  Non-shared disk

   -  By default, **/var/lib/docker**, **containerd**, or **/mnt/paas/kubernetes/kubelet** is mounted to CCE nodes. Check whether **/var**, **/var/lib**, **/mnt**, **/mnt/paas**, and **/mnt/paas/kubernetes** have been manually mounted.
   -  The soft link of **/var/lib/kubelet** to **/mnt/paas/kubernetes/kubelet** is created for CCE by default. Check whether it has been manually modified.

-  Shared disk

   -  By default, **/mnt/paas/** is mounted to CCE nodes. Check whether **/mnt** has been manually mounted.
   -  The soft link of **/var/lib/kubelet** to **/mnt/paas/kubernetes/kubelet**, or **/var/lib/docker** or **containerd** to **/mnt/paas/runtime** is created for CCE by default. Check whether the soft links have been manually modified.

Solution
--------

**How Do I Check Whether a Disk Is Shared?**

#. Log in to the target node based on the check information.

#. Run the **lsblk** command to check whether **vgpaas-share** is mounted to **/mnt/paas**. If yes, a shared disk is used.


   .. figure:: /_static/images/en-us_image_0000001851586680.png
      :alt: **Figure 1** Checking whether a shared disk is used

      **Figure 1** Checking whether a shared disk is used

**What Can I Do If an Error Occurred in a Node Mounting Check?**

#. Cancel the manually modified mount point.
#. Cancel the modification on the default soft link.
