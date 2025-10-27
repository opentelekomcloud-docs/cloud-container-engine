:original_name: cce_10_0848.html

.. _cce_10_0848:

Manually Upgrading the Driver Version of a GPU Node
===================================================

You can use the CCE AI Suite (NVIDIA GPU) add-on to configure the driver file path for a node. After the node is restarted, the driver will be installed automatically. Alternatively, manually upgrade the driver version.

.. note::

   Manually upgrading the driver version of a GPU node is a short-term fix for customizing node configurations. However, once the node is restarted, the driver version will revert to the version specified in the CCE AI Suite (NVIDIA GPU) settings.

   For a stable, long-term upgrade of the driver version of a GPU node, see :ref:`Upgrading the Driver Version of a GPU Node Using a Node Pool <cce_10_0849>`.

Prerequisites
-------------

The cluster can be accessed using kubectl. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

Procedure
---------

To use a specific NVIDIA driver version, perform the following operations to install the GPU driver of the latest version on the node:

#. Take the node offline and manually evict pods on the node to prevent all programs from using GPUs.

   a. Run the following command to take the node offline:

      .. code-block::

         kubectl cordon <NODE_NAME>

      After the node goes offline, it will no longer support pod scheduling. You can verify this by running the following command:

      .. code-block::

         kubectl get node <NODE_NAME>

      If the following information is displayed, the node does not allow pod scheduling:

      .. code-block::

         NAME            STATUS                     ROLES    AGE   VERSION
         192.168.1.xx    Ready,SchedulingDisabled   <none>   20m   v1.25.5-r20-25.1.31.1

   b. Manually evict pods on the node.

      .. code-block::

         kubectl drain 192.168.1.xx --ignore-daemonsets=true --delete-emptydir-data

      Expected result

      .. code-block::

         node/192.168.1.xx drained

   c. Check whether there are any DaemonSet pods are using GPUs. If so, stop kubelet and the runtime.

      Log in to the node where the GPU driver needs to be upgraded, for example, the node with IP address 192.168.1.xx.

      -  Stop containerd.

         .. code-block::

            systemctl stop kubelet kubelet-monit containerd containerd-monit

      -  Stop Docker.

         .. code-block::

            systemctl stop kubelet kubelet-monit docker docker-monit

   d. Check if any program is using GPUs. If so, evict it.

      #. Run the following command to check if any program is using GPUs:

         .. code-block::

            sudo fuser -v /dev/nvidia*

         If the following information is displayed, some programs are using the GPUs. If no fuser command is unavailable (for example, in an RPM-based Linux distribution), run the **yum install psmisc** command to install the Psmisc package:

         .. code-block::

                                 USER        PID ACCESS COMMAND
            /dev/nvidia0:        root      12192 F.... nvidia-gpu-devi
            /dev/nvidiactl:      root      12192 F.... nvidia-gpu-devi

      #. Delete the detected process. In this example, the process ID is 12192. Replace it with the actual one.

         .. code-block::

            sudo kill 12192

      #. After deleting the detected programs, run the following command again to verify:

         .. code-block::

            sudo fuser -v /dev/nvidia*      # Recheck if any program is using GPUs.

         If no process is displayed in the command output, the detected processes have been evicted, and no program is using the GPUs.

#. Install the NVIDIA driver of a specified version on the node.

   a. Download the driver of the specified version at the `official NVIDIA website <https://www.nvidia.com/download/index.aspx>`__. For details about how to select a proper driver version, see :ref:`Selecting a GPU Driver Version for Nodes <cce_10_0846>`.

   b. Record the driver information of the current version. The command for obtaining a driver varies depending on the CCE AI Suite (NVIDIA GPU) add-on version.

      -  For v1.x.x, run the following command:

         .. code-block::

            /opt/cloud/cce/nvidia/bin/nvidia-smi

      -  For v2.0.0 through v2.5.3, run the following command:

         .. code-block::

            /usr/local/nvidia/bin/nvidia-smi

      -  For versions later than v2.5.4, run the following command:

         .. code-block::

            nvidia-smi

      The execution results of the preceding three versions are basically the same. The command output is as follows:

      .. code-block::

         Tue Feb 20 15:06:54 2024
         +-----------------------------------------------------------------------------+
         | NVIDIA-SMI 470.141.03   Driver Version: 470.141.03   CUDA Version: 11.4     |
         |-------------------------------+----------------------+----------------------+
         | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
         | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
         |                               |                      |               MIG M. |
         |===============================+======================+======================|
         |   0  Tesla V100-SXM2...  Off  | 00000000:21:01.0 Off |                    0 |
         | N/A   31C    P0    23W / 300W |      0MiB / 16160MiB |      0%      Default |
         |                               |                      |                  N/A |
         +-------------------------------+----------------------+----------------------+

         +-----------------------------------------------------------------------------+
         | Processes:                                                                  |
         |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
         |        ID   ID                                                   Usage      |
         |=============================================================================|
         |  No running processes found                                                 |
         +-----------------------------------------------------------------------------+

   c. Prepare for the installation.

      Obtain the kernel version of the current node and install its dependencies (such as gcc, make, and kernel-devel). The following is an example using a RPM-based Linux distribution (whose OS is EulerOS, HCE, or CentOS).

      .. code-block::

         yum install -y perl kernel-devel-$(uname -r) zlib-devel binutils binutils-extra binutils-devel elfutils-libelf-devel gcc make

   d. Install the downloaded driver and verify the installation.

      The **NVIDIA-Linux-x86_64-535.54.03.run** driver is used as an example.

      .. code-block::

         # Obtain the kernel version.
         kerVersion=`uname -r`

         # Logs for installation details
         touch /root/nvidia-installer.log

         # Install the driver downloaded at the official NVIDIA website.
         sh NVIDIA-Linux-x86_64-535.54.03.run --silent --kernel-source-path=/usr/src/kernels/$kerVersion --log-file-name=/root/nvidia-installer.log --no-install-compat32-libs --utility-prefix="/usr/local/nvidia" --opengl-prefix="/usr/local/nvidia"

      .. note::

         If the following information is displayed during the installation:

         .. code-block::

            ERROR: An NVIDIA kernel module 'nvidia' appears to already be loaded in your kernel.  This may be because it is in use (for example, by an X server, a CUDA program, or the NVIDIA Persistence Daemon), but this may also happen if your kernel was configured without support for module unloading.  Please be sure to exit any programs that may be using the GPU(s) before attempting to upgrade your driver.  If no GPU-based programs are running, you know that your kernel supports module unloading, and you still receive this message, then an error may have occurred that has corrupted an NVIDIA kernel module's usage count, for which the simplest remedy is to reboot your computer.
            ERROR: Installation has failed.  Please see the file '/root/nvidia-installer.log' for details.  You may find suggestions on fixing installation problems in the README available on the Linux driver download page at www.nvidia.com.

         Perform the following operations to handle this issue:

         #. Disable kubelet and the runtime.

            -  Disable containerd.

               .. code-block::

                  systemctl disable kubelet kubelet-monit containerd containerd-monit

            -  Disable Docker.

               .. code-block::

                  systemctl disable kubelet kubelet-monit docker docker-monit

         #. Restart the affected node.
         #. Run the driver installation command again.
         #. Enable kubelet and the runtime.

            -  Enable containerd.

               .. code-block::

                  systemctl enable kubelet kubelet-monit containerd containerd-monit

            -  Enable Docker.

               .. code-block::

                  systemctl enable kubelet kubelet-monit docker docker-monit

   e. Check whether the new version of the GPU driver is installed.

      -  For v1.x.x, run the following command:

         .. code-block::

            /opt/cloud/cce/nvidia/bin/nvidia-smi

      -  For v2.0.0 through v2.5.3, run the following command:

         .. code-block::

            /usr/local/nvidia/bin/nvidia-smi

      -  For versions later than v2.5.4, run the following command:

         .. code-block::

            nvidia-smi

      If the following information is displayed, the new version of the GPU driver has been installed:

      .. code-block::

         Tue Feb 20 15:13:58 2024
         +---------------------------------------------------------------------------------------+
         | NVIDIA-SMI 535.54.03              Driver Version: 535.54.03    CUDA Version: 12.2     |
         |-----------------------------------------+----------------------+----------------------+
         | GPU  Name                 Persistence-M | Bus-Id        Disp.A | Volatile Uncorr. ECC |
         | Fan  Temp   Perf          Pwr:Usage/Cap |         Memory-Usage | GPU-Util  Compute M. |
         |                                         |                      |               MIG M. |
         |=========================================+======================+======================|
         |   0  Tesla V100-SXM2-16GB           Off | 00000000:21:01.0 Off |                    0 |
         | N/A   34C    P0              38W / 300W |      0MiB / 16384MiB |      2%      Default |
         |                                         |                      |                  N/A |
         +-----------------------------------------+----------------------+----------------------+

         +---------------------------------------------------------------------------------------+
         | Processes:                                                                            |
         |  GPU   GI   CI        PID   Type   Process name                            GPU Memory |
         |        ID   ID                                                             Usage      |
         |=======================================================================================|
         |  No running processes found                                                           |
         +---------------------------------------------------------------------------------------+

   f. Delete driver files and historical commands.

      .. code-block::

         rm NVIDIA-Linux-x86_64-535.54.03.run /root/nvidia-installer.log && history -c

#. Restart the node.

   a. Restart kubelet and the runtime.

      -  Restart containerd.

         .. code-block::

            systemctl start kubelet kubelet-monit containerd containerd-monit

      -  Restart Docker.

         .. code-block::

            systemctl start kubelet kubelet-monit docker docker-monit

   b. Remove scheduling restrictions on the node.

      .. code-block::

         kubectl uncordon <NODE_NAME>
