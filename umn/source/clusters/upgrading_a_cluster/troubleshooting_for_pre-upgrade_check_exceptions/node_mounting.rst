:original_name: cce_10_0498.html

.. _cce_10_0498:

Node Mounting
=============

Check Items
-----------

This section describes how to diagnose and fix node-mount failures caused by CCE storage misconfigurations.

Solution
--------

-  **Check item 1**: mount point conflicts

   **Check Content**

   Verify that all required paths are mounted as expected. CCE needs to use the subdirectories of these paths. If the paths have been mounted, they are inaccessible to CCE nodes. In this case, an exception may occur when the node is restarted or a node component is restarted during an upgrade.

   If there is a **vgpaas-share** volume group (shared disk), check only the **/mnt** path.

   If there is no **vgpaas-share** volume group (not a shared disk), check the **/mnt**, **/mnt/paas**, **/mnt/paas/kubernetes**, **/var**, and **/var/lib** paths.

   **Solution**

   If the error message "the file path xxx is mounted, but the subpath is used by the CCE" is displayed, a path is mounted but CCE still needs to access its subpath. Check the node's pre-installation script for any mount commands that target the affected path. Remove the command or remount to an unused path. Then, drain and reset the node.

-  **Check item 2: symlinks and bind mounts**

   **Check Content**

   Verify that the following key directories are correctly configured:

   #. General check: Check whether **/var/lib/kubelet** is linked to **/mnt/paas/kubernetes/kubelet**.
   #. Conditional check: when the **vgpaas-share** volume group (shared disk) is used

      -  If Docker is used, check whether **/var/lib/docker** is linked to **/mnt/paas/runtime**.
      -  If containerd is used, check whether **/var/lib/containerd** is linked to **/mnt/paas/runtime**.

   **Solution**

   If the error message "the target path of the path link xxx" is displayed, the symlink either points to nothing or to a wrong location. If **ls** on the symlink target returns content, :ref:`drain <cce_10_0605>` and :ref:`reset <cce_10_0003>` the node. Use the fixes below only if the symlink or mount point is empty. The following uses **/var/lib/kubelet** as an example:

   -  **Case 1: A symlink is missing.**

      Create a symlink.

      .. code-block::

         sudo ln -sf /mnt/paas/kubernetes/kubelet /var/lib/kubelet

   -  **Case 2: A symlink points to a wrong location.**

      #. Remove the incorrect symlink.

         .. code-block::

            sudo rm /var/lib/kubelet

      #. Create a correct symlink.

         .. code-block::

            sudo ln -sf /mnt/paas/kubernetes/kubelet /var/lib/kubelet

Common Issues
-------------

**How Do I Check Whether the vgpaas-share Volume Group (Shared Disk) Exists?**

#. Log in to the target node based on the check information.

#. Run the **lsblk** command to check whether **vgpaas-share** is mounted to **/mnt/paas**. If yes, a shared disk is used.


   .. figure:: /_static/images/en-us_image_0000002467718381.png
      :alt: **Figure 1** Checking whether a shared disk is used

      **Figure 1** Checking whether a shared disk is used
