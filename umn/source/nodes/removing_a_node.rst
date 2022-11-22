:original_name: cce_01_0338.html

.. _cce_01_0338:

Removing a Node
===============

Scenario
--------

Removing a node from a cluster in CCE will re-install the node OS and clear CCE components on the node.

Removing a node will not delete the server (ECS) corresponding to the node. You are advised to remove nodes at off-peak hours to avoid impacts on your services.

After a node is removed from the cluster, the node is still running and incurs fees.

Notes and Constraints
---------------------

-  Nodes can be removed only when the cluster is in the Available or Unavailable state.
-  A CCE node can be removed only when it is in the Active, Abnormal, or Error state.
-  A CCE node in the Active state can have its OS re-installed and CCE components cleared after it is removed.
-  If the OS fails to be re-installed after the node is removed, manually re-install the OS. After the re-installation, log in to the node and run the clearance script to clear CCE components. For details, see :ref:`Handling Failed OS Reinstallation <cce_01_0338__section149069481111>`.

Precautions
-----------

-  Removing a node will lead to pod migration, which may affect services. Perform this operation during off-peak hours.
-  Unexpected risks may occur during the operation. Back up data in advance.
-  While the node is being deleted, the backend will set the node to the unschedulable state.
-  After you remove the node and re-install the OS, the original LVM partitions will be cleared and the data managed by LVM will be cleared. Therefore, back up data in advance.

Procedure
---------

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Nodes**. In the same row as the target node, choose **More** > **Remove**.

#. In the dialog box displayed, enter **REMOVE**, configure the login information required for re-installing the OS, and click **Yes**. Wait until the node is removed.

   After the node is removed, workload pods on the node are automatically migrated to other available nodes.

.. _cce_01_0338__section149069481111:

Handling Failed OS Reinstallation
---------------------------------

You can perform the following steps to re-install the OS and clear the CCE components on the node if previous attempts fail:

#. Log in to the management console of the server and re-install the OS.

#. Log in to the server and run the following commands to clear the CCE components and LVM data:

   Write the following scripts to the **clean.sh** file:

   .. code-block::

      lsblk
      vgs --noheadings | awk '{print $1}' | xargs vgremove -f
      pvs --noheadings | awk '{print $1}' | xargs pvremove -f
      lvs --noheadings | awk '{print $1}' | xargs -i lvremove -f --select {}
      function init_data_disk() {
          all_devices=$(lsblk -o KNAME,TYPE | grep disk | grep -v nvme | awk '{print $1}' | awk '{ print "/dev/"$1}')
          for device in ${all_devices[@]}; do
              isRootDisk=$(lsblk -o KNAME,MOUNTPOINT $device 2>/dev/null| grep -E '[[:space:]]/$' | wc -l )
              if [[ ${isRootDisk} != 0 ]]; then
                  continue
              fi
              dd if=/dev/urandom of=${device} bs=512 count=64
              return
          done
          exit 1
      }
      init_data_disk
      lsblk

   Run the following command:

   **bash clean.sh**
