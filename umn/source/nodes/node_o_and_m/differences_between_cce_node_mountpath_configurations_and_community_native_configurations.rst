:original_name: cce_10_0883.html

.. _cce_10_0883:

Differences Between CCE Node mountPath Configurations and Community Native Configurations
=========================================================================================

To maintain the stability of nodes, CCE stores Kubernetes and container runtime components on separate data disks. Kubernetes uses the **/mnt/paas/kubernetes** directory, and the container runtime uses the **/mnt/paas/runtime** directory. The paths to these directories are the same as the default paths used in the community through soft links.

Configuration Differences Between CCE and Community Native
----------------------------------------------------------

+--------------------------------+------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+
| Item                           | Kubernetes Native Path | CCE Path                                                                                                                                  |
+================================+========================+===========================================================================================================================================+
| kubelet **root-dir**           | /var/lib/kubelet       | /mnt/paas/kubernetes/kubelet                                                                                                              |
+--------------------------------+------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+
| kubelet                        | /var/lib/kubelet       | /mnt/paas/kubernetes/kubelet                                                                                                              |
|                                |                        |                                                                                                                                           |
|                                |                        | The soft link from **/var/lib/kubelet** to **/mnt/paas/kubernetes/kubelet** is also available.                                            |
+--------------------------------+------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+
| Container runtime (Docker)     | /var/lib/docker        | -  :ref:`Data Disk Space Allocation <cce_10_0341>` set to specific: /var/lib/docker, which is identical to the Kubernetes native path     |
+--------------------------------+------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+
| Container runtime (containerd) | /var/lib/containerd    | -  :ref:`Data Disk Space Allocation <cce_10_0341>` set to specific: /var/lib/containerd, which is identical to the Kubernetes native path |
+--------------------------------+------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+
| containerd logs                | /var/log/pods          | /var/lib/containerd/container_logs                                                                                                        |
|                                |                        |                                                                                                                                           |
|                                |                        | The soft link from **/var/log/pods** to **/var/lib/containerd/container_logs** is also available.                                         |
+--------------------------------+------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+

.. note::

   In clusters of v1.23.16-r0, v1.25.11-r0, v1.27.8-r0, v1.28.6-r0, v1.29.2-r0, or later versions, modified soft links will be mounted in both Kubernetes and CCE paths.

Possible Impact of Differences
------------------------------

-  If a soft link file is mounted into a container, the real path that the soft link file points to cannot be accessed.

   For example, if **/var/log** is mounted to **/mnt/log** in a container through a hostPath, **/mnt/log/pods** is an abnormal soft link file in the container, and the actual data in **/var/log/pods** cannot be accessed.

   Mount the actual file path to the container to prevent a file read failure caused by soft links.

-  The kubelet **root-dir** path (**/mnt/paas/kubernetes/kubelet**) is inconsistent with the community path (**/var/lib/kubelet**). If the container mount path of a third-party CSI add-on is the community path, the file mounting does not take effect.

   For example, when Vault (an open-source third-party add-on) uses secrets-store-csi-driver to mount a secret, if the **root-dir** path to the add-on is inconsistent with the CCE configuration path (the default value of the add-on is the same as the community path **/var/lib/kubelet**), the Vault secret cannot be obtained in the container.

   This is because CSI uses mountPropagation to mount volumes to target containers. kubelet includes the mount path (**/mnt/paas/kubernetes/kubelet/pods/{podID}/volume/...**) related to kubelet **root-dir** into the CSI request for mounting a volume. When the CSI container mounts a volume to the mount path requested by kubelet CSI, if the mount path is irrelevant to the hostPath, the mount propagation will be invalid. For example, the CSI container mounts host **/var/lib/kubelet** to container **/var/lib/kubelet**, and **/mnt/paas/kubernetes/kubelet/pods/{podID}/volume/...** in the CSI container is irrelevant to the hostPath.

   Update the **root-dir** path in CSI to match the kubelet **root-dir** path on the current CCE node.
