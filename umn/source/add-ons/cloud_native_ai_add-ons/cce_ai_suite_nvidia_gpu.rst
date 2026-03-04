:original_name: cce_10_0141.html

.. _cce_10_0141:

CCE AI Suite (NVIDIA GPU)
=========================

Introduction
------------

The CCE AI Suite (NVIDIA GPU) add-on helps you use and manage GPUs in your clusters. It supports access to GPUs in containers and helps you efficiently run and maintain GPU-based compute-intensive workloads in cloud native environments. With this add-on, both CCE standard and Turbo clusters can handle GPU scheduling, install drivers automatically, manage runtimes, and monitor performance. This means you get full support for GPU workloads throughout their entire lifecycle. To run GPU nodes in a cluster, you must install this add-on.

How nvidia-gpu-device-plugin Works
----------------------------------

nvidia-gpu-device-plugin is one of the core components of CCE AI Suite (NVIDIA GPU). As a bridge between the container platform and GPU hardware, nvidia-gpu-device-plugin abstracts physical GPUs into resources that can be identified and scheduled by the container platform. This addresses the GPU allocation and usage problems in containerized environments.

This component runs as a DaemonSet so that the GPUs on every node can be identified by kubelet and reported to Kubernetes. You only need to declare the GPU resource requests in pod specs to use the GPUs on nodes. kubelet does not automatically detect GPU devices on its own. Instead, it relies on nvidia-gpu-device-plugin to register and report GPU information. nvidia-gpu-device-plugin runs on every GPU node and uses the gRPC protocol to communicate with kubelet Device Plugin Manager and complete the registration. The workflow is shown below.


.. figure:: /_static/images/en-us_image_0000002516079373.png
   :alt: **Figure 1** GPU resource reporting and health

   **Figure 1** GPU resource reporting and health

#. Sending a registration request: nvidia-gpu-device-plugin sends a registration request to kubelet as a client along with:

   -  Device name (**nvidia.com/gpu**): identifies the type of hardware resource managed by the add-on for kubelet to identify and schedule.
   -  Unix socket: enables local gRPC communication between the component and kubelet to ensure that kubelet can properly call the correct services.
   -  API version: specifies the version of the Device Plugin API protocol. It ensures that the communication protocols of both parties are compatible.

#. Starting a service: After registration, nvidia-gpu-device-plugin starts a gRPC server to provide services for external systems. The gRPC server handles kubelet requests, including device list queries, health status reporting, and resource allocation. The listening address (Unix socket path) of the gRPC server and supported Device Plugin API version have been reported to kubelet during registration. This ensures that kubelet can properly establish connections and call the correct APIs based on the registration information.
#. Health monitoring: After the gRPC server is started, kubelet establishes a persistent connection with nvidia-gpu-device-plugin through the ListAndWatch API to continuously listen to the device IDs and their health. If a device becomes unhealthy, nvidia-gpu-device-plugin reports the error to kubelet through the connection.
#. Information reporting: kubelet integrates the device information into the node statuses and reports resource details such as the number of devices to Kubernetes API server. The scheduler (kube-scheduler or Volcano) uses these details to make scheduling decisions.
#. Persistent storage: CCE stores the GPU device information (such as quantity and status) reported by nodes in etcd for cluster-level resource persistence. This ensures that GPU data can be kept after a cluster component is faulty or restarted, and provides consistent data sources for components such as the scheduler and controller, ensuring reliability of resource scheduling and management.

Notes and Constraints
---------------------

-  The driver to be downloaded must be a **.run** file.
-  Only NVIDIA Tesla drivers are supported, not GRID drivers.
-  When installing or reinstalling the add-on, ensure that the driver download address is correct and accessible. CCE does not verify the address validity.
-  This add-on only enables you to download the driver and execute the installation script. The add-on status only indicates that how the add-on is running, not whether the driver is successfully installed.
-  CCE does not guarantee the compatibility between the GPU driver version and the CUDA library version of your application. You need to check the compatibility by yourself.
-  If a custom OS image has had a GPU driver installed, CCE cannot ensure that the GPU driver is compatible with other GPU components such as the monitoring components used in CCE.

.. _cce_10_0141__section14115341020:

Installing the Add-on
---------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Add-ons**. In the right pane, find the CCE AI Suite (NVIDIA GPU) add-on and click **Install**.
#. Determine whether to enable **Use DCGM-Exporter to Observe DCGM Metrics**. After this function is enabled, DCGM-Exporter is deployed on the GPU node.

   .. important::

      If the add-on version is 2.7.40 or later, DCGM-Exporter can be deployed. DCGM-Exporter maintains the community capability and does not support the sharing mode or GPU virtualization.

#. Configure the add-on parameters.

   .. table:: **Table 1** Add-on parameters

      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                            |
      +===================================+========================================================================================================================================================================================================================================================================================================================================+
      | Default Cluster Driver            | All GPU nodes in a cluster use the same driver. You can select a proper GPU driver version or customize the driver link and enter the download link of the NVIDIA driver.                                                                                                                                                              |
      |                                   |                                                                                                                                                                                                                                                                                                                                        |
      |                                   | .. important::                                                                                                                                                                                                                                                                                                                         |
      |                                   |                                                                                                                                                                                                                                                                                                                                        |
      |                                   |    NOTICE:                                                                                                                                                                                                                                                                                                                             |
      |                                   |                                                                                                                                                                                                                                                                                                                                        |
      |                                   |    -  If the download link is a public network address, for example, **https://us.download.nvidia.com/tesla/470.103.01/NVIDIA-Linux-x86_64-470.103.01.run**, bind an EIP to each GPU node. For details about how to obtain the driver link, see :ref:`Obtaining a Driver Link from the Internet <cce_10_0141__section95451728192112>`. |
      |                                   |    -  If the download link is an OBS URL, you do not need to bind an EIP to GPU nodes. For details about how to obtain the driver link, see :ref:`Obtaining a Driver Link from the OBS Link <cce_10_0141__section14922133914508>`.                                                                                                     |
      |                                   |    -  Ensure that the NVIDIA driver version matches the GPU node.                                                                                                                                                                                                                                                                      |
      |                                   |    -  If the driver version is changed, restart the node to apply the change.                                                                                                                                                                                                                                                          |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. note::

      After the add-on is installed, you can configure GPU virtualization and node pool drivers on the **Heterogeneous Resources** tab in **Settings**.

#. Click **Install**.

   .. note::

      If the add-on is uninstalled, GPU pods newly scheduled to the nodes cannot run properly, but GPU pods already running on the nodes will not be affected.

Verifying the Add-on
--------------------

After the add-on is installed, run the **nvidia-smi** command on the GPU node and the container that schedules GPU resources to verify the availability of the GPU device and driver.

-  GPU node:

   -  If the add-on version is earlier than 2.0.0, run the following command:

      .. code-block::

         cd /opt/cloud/cce/nvidia/bin && ./nvidia-smi

   -  If the add-on version is 2.0.0 or later, run the following command:

      .. code-block::

         cd /usr/local/nvidia/bin && ./nvidia-smi

-  Container:

   -  If the cluster version is v1.27 or earlier, run the following command:

      .. code-block::

         cd /usr/local/nvidia/bin && ./nvidia-smi

   -  If the cluster version is v1.28 or later, run the following command:

      .. code-block::

         cd /usr/bin && ./nvidia-smi

If GPU information is returned, the device is available and the add-on has been installed.

|image1|

Managing the Add-on
-------------------

Once the add-on is installed, you can upgrade or roll back it as needed. Before upgrading or rolling back the CCE AI Suite (NVIDIA GPU) add-on, make sure there are no GPU virtualization workloads running on the GPU node. If the GPU node has GPU virtualization workloads, when you upgrade or roll back the add-on, you need to drain the GPU node.

Upgrading the Add-on
--------------------

CCE regularly launches new add-on versions to update features, improve performance, and address bugs. The upgrade procedure is as follows:

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Add-ons**. In the right pane, find the CCE AI Suite (NVIDIA GPU) add-on and click **Upgrade**.

#. In the window that slides out from the right, click the version number next to **Add-on Versions** and select the target version from the drop-down list. Set other parameters as required. For details, see :ref:`Installing the Add-on <cce_10_0141__section14115341020>`. If you need to change the **Default Cluster Driver** setting, manually restart the GPU node after the add-on upgrade is complete. For details, see "Upgrading a GPU Driver."

   After all parameters are configured, click **OK** in the lower right corner. If the add-on status transitions from **Upgrading** to **Running**, the upgrade is successful.

Rolling Back the Add-on
-----------------------

If an add-on upgrade fails or any other exceptions occur, you have the option to roll back the add-on to the source version. After an add-on rollback, **the virtualization and driver configurations of node pools remain unchanged and are not reverted**. The add-on rollback feature has specific requirements based on the cluster and add-on versions.

-  If the cluster version is v1.28 or later, only add-on of 2.7.35 or later versions can be rolled back. (The source version must be later than 2.7.13).
-  If the cluster version is v1.27 or earlier, only add-on of 2.1.19 or later versions can be rolled back. (The source version must be later than 2.1.8).

.. note::

   If you have modified the add-on startup parameters, check and delete the custom parameters before the rollback. If you do not perform these operations, the rollback may fail because the target version does not support these parameters.

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Add-ons**. In the right pane, find the CCE AI Suite (NVIDIA GPU) add-on and click **Roll Back**.
#. In the **Roll Back Add-on** dialog box displayed, check that the target version is correct and click **Yes**. If the add-on status transitions from **Rolling back** to **Running**, the rollback is successful.

Upgrading a GPU Driver
----------------------

When the CUDA library in use is incompatible with an NVIDIA driver version, the associated GPU node becomes unavailable. To resolve this, you need to upgrade the driver version. Both CCE standard and Turbo clusters provide support for driver upgrades at the cluster and node pool levels. This section covers the procedure for upgrading a cluster driver. For details about how to upgrade a node pool driver, see :ref:`Upgrading the Driver Version of a GPU Node Using a Node Pool <cce_10_0849>`.

Upgrading the NVIDIA driver of a cluster involves reinstalling the driver during the node restart. After specifying the desired cluster driver version on the console, you must manually restart the GPU node. To do so, perform the following operations:

#. Specify the cluster driver version. You can specify the cluster driver version through either of the following ways:

   -  **Upgrading the add-on**: In the navigation pane, choose **Add-ons**. In the right pane, find the CCE AI Suite (NVIDIA GPU) add-on and click **Upgrade**. In the window that slides out from the right, change the value of **Cluster Default Driver** to a proper one, for example, **535.216.03**, and click **OK**.
   -  **Editing the add-on**: In the navigation pane, choose **Add-ons**. In the right pane, find the CCE AI Suite (NVIDIA GPU) add-on and click **Edit**. In the window that slides out from the right, change the value of **Cluster Default Driver** to a proper one, for example, **535.216.03**, and click **OK**.
   -  **Configuring heterogeneous resources**: In the navigation pane, choose **Settings**. In the right pane, click the **Heterogeneous Resources** tab, change the value of **Cluster Default Driver** to a proper one, for example, **535.216.03**, and click **Confirm Settings**.

#. Perform rolling restart on the GPU node and install the driver of the specified version. **Evict pods on the GPU node before restarting the node.** For details, see :ref:`Draining a Node <cce_10_0605>`. When draining a GPU node, make sure to reserve enough GPU resources on other nodes to meet pod scheduling needs. This helps avoid pod scheduling issues due to inadequate resources and ensures smooth service operation.

   a. In the navigation pane, choose **Nodes**. In the right pane, click the **Nodes** tab and view all GPU nodes.
   b. Click the GPU node name. On the page displayed, click **Restart** in the upper right corner. After the GPU node is restarted, wait for 5 to 10 minutes for the driver installation to complete. You can check the CCE AI Suite (NVIDIA GPU) add-on status to see whether the driver has been installed. If the add-on status changes to **Running**, the driver has been installed.

#. Log in to the node, verify the version of the driver installed on it, and confirm that the GPU node driver upgrade was successful. The command for obtaining a driver version varies based on the CCE AI Suite (NVIDIA GPU) version.

   -  For v1.x.x, run the following command:

      .. code-block::

         /opt/cloud/cce/nvidia/bin/nvidia-smi

   -  For v2.0.0 through v2.5.3, run the following command:

      .. code-block::

         /usr/local/nvidia/bin/nvidia-smi

   -  For versions later than v2.5.4, run the following command:

      .. code-block::

         nvidia-smi

   The following command output is displayed. The node driver version is 535.216.03 and matches the specified version. This indicates that the upgrade has been completed.

   |image2|

   Once you have verified that the node and services are operating properly, follow the preceding steps again to perform a restart of the remaining GPU nodes within the cluster.

Supported GPU Drivers
---------------------

.. important::

   -  The list of supported GPU drivers applies only to CCE AI Suite (NVIDIA GPU) of v1.2.28 or later.

   -  To use the latest GPU driver, upgrade your CCE AI Suite (NVIDIA GPU) to the latest version.

   -  The compatibility of the OSs, GPU drivers, and GPU models listed in :ref:`Table 2 <cce_10_0141__table056413288409>` has been tested and verified. To ensure excellent performance and stability, use these GPU drivers. If the deployment is performed in an unverified environment, perform full tests based on the environment to ensure the compatibility and stability.

   -  NVIDIA no longer provides updates or security patches for GPU drivers that have reached their end of life (EOL). For details, see `Driver Lifecycle <https://docs.nvidia.com/datacenter/tesla/drivers/#driver-lifecycle>`__. For example, a Production Branch (PB) provides one-year support from the date of release, and a Long-Term Support Branch (LTSB) provides three-year support.

      According to this policy, CCE does not provide technical support for GPU drivers that have reached EOL, including driver installation and updates. **The following drivers have reached EOL: 510.47.03, 470.141.03, and 470.57.02.**

   -  When installing a GPU driver on Ubuntu and CentOS, pay attention to the OS version. For details, see :ref:`Table 3 <cce_10_0141__table6519152112818>`. For more information, see `NVIDIA Driver Documentation <https://docs.nvidia.com/datacenter/tesla/>`__.

.. _cce_10_0141__table056413288409:

.. table:: **Table 2** Supported GPU drivers

   +------------+------------------------+---------------+------------+--------------+---------------------+
   | GPU Model  | Supported Cluster Type | Specification | OS         | OS           | OS                  |
   +============+========================+===============+============+==============+=====================+
   |            |                        |               | HCE OS 2.0 | Ubuntu 22.04 | EulerOS release 2.9 |
   +------------+------------------------+---------------+------------+--------------+---------------------+
   | Tesla T4   | CCE standard cluster   | g6            | 570.86.15  | 570.86.15    | 535.54.03           |
   |            |                        |               |            |              |                     |
   |            |                        | pi2           | 535.216.03 | 535.216.03   | 470.141.03          |
   |            |                        |               |            |              |                     |
   |            |                        |               | 535.161.08 | 535.161.08   |                     |
   |            |                        |               |            |              |                     |
   |            |                        |               | 535.54.03  | 535.54.03    |                     |
   |            |                        |               |            |              |                     |
   |            |                        |               | 510.47.03  |              |                     |
   |            |                        |               |            |              |                     |
   |            |                        |               | 470.57.02  |              |                     |
   +------------+------------------------+---------------+------------+--------------+---------------------+
   | Tesla V100 | CCE standard cluster   | p2s           | 570.86.15  | 570.86.15    | 535.54.03           |
   |            |                        |               |            |              |                     |
   |            |                        | p2vs          | 535.216.03 | 535.216.03   | 470.141.03          |
   |            |                        |               |            |              |                     |
   |            |                        | p2v           | 535.161.08 | 535.161.08   |                     |
   |            |                        |               |            |              |                     |
   |            |                        |               | 535.54.03  | 535.54.03    |                     |
   |            |                        |               |            |              |                     |
   |            |                        |               | 510.47.03  |              |                     |
   |            |                        |               |            |              |                     |
   |            |                        |               | 470.57.02  |              |                     |
   +------------+------------------------+---------------+------------+--------------+---------------------+

.. _cce_10_0141__table6519152112818:

.. table:: **Table 3** Mapping between GPU driver versions and OS versions

   +-------------------------------------------------------------------------------------------------------------------------------+-----------------------------------+
   | Driver Version                                                                                                                | Ubuntu 22.04                      |
   +===============================================================================================================================+===================================+
   | `570.86.15 <https://docs.nvidia.com/datacenter/tesla/tesla-release-notes-570-86-15/index.html#hardware-software-support>`__   | Ubuntu 22.04.z LTS (where z <= 5) |
   +-------------------------------------------------------------------------------------------------------------------------------+-----------------------------------+
   | `535.216.03 <https://docs.nvidia.com/datacenter/tesla/tesla-release-notes-535-216-03/index.html#hardware-software-support>`__ | Ubuntu 22.04.z LTS (where z <= 4) |
   +-------------------------------------------------------------------------------------------------------------------------------+-----------------------------------+
   | `535.161.08 <https://docs.nvidia.com/datacenter/tesla/tesla-release-notes-535-161-08/index.html#hardware-software-support>`__ | Ubuntu 22.04.z LTS (where z <= 3) |
   +-------------------------------------------------------------------------------------------------------------------------------+-----------------------------------+
   | `535.54.03 <https://docs.nvidia.com/datacenter/tesla/tesla-release-notes-535-54-03/index.html#hardware-software-support>`__   | Ubuntu 22.04.z LTS (where z <= 2) |
   +-------------------------------------------------------------------------------------------------------------------------------+-----------------------------------+
   | `510.47.03 <https://docs.nvidia.com/datacenter/tesla/tesla-release-notes-510-47-03/index.html#hardware-software-support>`__   | Not supported                     |
   +-------------------------------------------------------------------------------------------------------------------------------+-----------------------------------+
   | `470.141.03 <https://docs.nvidia.com/datacenter/tesla/tesla-release-notes-470-141-03/index.html#hardware-software-support>`__ | Not supported                     |
   +-------------------------------------------------------------------------------------------------------------------------------+-----------------------------------+
   | `470.57.02 <https://docs.nvidia.com/datacenter/tesla/tesla-release-notes-470-57-02/index.html#hardware-software-support>`__   | Not supported                     |
   +-------------------------------------------------------------------------------------------------------------------------------+-----------------------------------+

.. _cce_10_0141__section95451728192112:

Obtaining a Driver Link from the Internet
-----------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Create a node. In the **Specifications** area, select the GPU node flavor. The GPU card models are displayed in the lower part of the area.

3. Log in to the `NVIDIA driver download page <https://www.nvidia.com/en-us/drivers/>`__ and search for the driver information. The OS must be **Linux 64-bit**.


   .. figure:: /_static/images/en-us_image_0000002516199373.png
      :alt: **Figure 2** Selecting parameters

      **Figure 2** Selecting parameters

4. After confirming the driver information, click **Find**. On the displayed page, find the driver to be downloaded and click **View**.


   .. figure:: /_static/images/en-us_image_0000002483959410.png
      :alt: **Figure 3** Viewing the driver information

      **Figure 3** Viewing the driver information

5. Click **Download** and copy the download link.


   .. figure:: /_static/images/en-us_image_0000002516079355.png
      :alt: **Figure 4** Obtaining the link

      **Figure 4** Obtaining the link

.. _cce_10_0141__section14922133914508:

Obtaining a Driver Link from the OBS Link
-----------------------------------------

#. Upload the driver to OBS and set the driver file to public read.

   .. note::

      When the node is restarted, the driver will be downloaded and installed again. Ensure that the OBS bucket link of the driver is valid.

#. In the bucket list, click a bucket name, and then the **Overview** page of the bucket is displayed.
#. In the navigation pane, choose **Objects**.
#. Select the name of the target object and copy the driver link on the object details page.

Components
----------

.. table:: **Table 4** Add-on components

   +--------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
   | Component                | Description                                                                                                                                                                      | Resource Type |
   +==========================+==================================================================================================================================================================================+===============+
   | nvidia-driver-installer  | A workload for installing the NVIDIA GPU driver on a node, which only uses resources during the installation process (Once the installation is finished, no resources are used.) | DaemonSet     |
   +--------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
   | nvidia-gpu-device-plugin | A Kubernetes device plugin that provides NVIDIA GPU heterogeneous compute for containers                                                                                         | DaemonSet     |
   +--------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
   | nvidia-operator          | A component that provides NVIDIA GPU node management capabilities for clusters                                                                                                   | Deployment    |
   +--------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+

Helpful Links
-------------

-  CCE AI Suite (NVIDIA GPU) provides GPU monitoring metrics. For details about GPU metrics, see :ref:`GPU Metrics <cce_10_0955>`.

Release History
---------------

.. table:: **Table 5** CCE AI Suite (NVIDIA GPU) add-on

   +-----------------------+---------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Add-on Version        | Supported Cluster Version | New Feature                                                                                                                                                                      |
   +=======================+===========================+==================================================================================================================================================================================+
   | 2.8.4                 | v1.28                     | Fixed `CVE-2025-23266 <https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2025-23266>`__ and `CVE-2025-23267 <https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2025-23267>`__. |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.29                     |                                                                                                                                                                                  |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.30                     |                                                                                                                                                                                  |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.31                     |                                                                                                                                                                                  |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.32                     |                                                                                                                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 2.7.66                | v1.28                     | Fixed some issues.                                                                                                                                                               |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.29                     |                                                                                                                                                                                  |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.30                     |                                                                                                                                                                                  |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.31                     |                                                                                                                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 2.7.63                | v1.28                     | Fixed the security vulnerabilities.                                                                                                                                              |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.29                     |                                                                                                                                                                                  |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.30                     |                                                                                                                                                                                  |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.31                     |                                                                                                                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 2.7.19                | v1.28                     | Fixed the nvidia-container-toolkit CVE-2024-0132 container escape vulnerability.                                                                                                 |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.29                     |                                                                                                                                                                                  |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.30                     |                                                                                                                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 2.7.13                | v1.28                     | -  Supported xGPU configuration by node pool.                                                                                                                                    |
   |                       |                           | -  Supported GPU rendering.                                                                                                                                                      |
   |                       | v1.29                     | -  Clusters v1.30 are supported.                                                                                                                                                 |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.30                     |                                                                                                                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 2.6.4                 | v1.28                     | Updated the isolation logic of GPU cards.                                                                                                                                        |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.29                     |                                                                                                                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 2.6.1                 | v1.28                     | Upgraded the base images of the add-on.                                                                                                                                          |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.29                     |                                                                                                                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 2.5.6                 | v1.28                     | Fixed an issue that occurred during the installation of the driver.                                                                                                              |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 2.5.4                 | v1.28                     | Clusters v1.28 are supported.                                                                                                                                                    |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 2.2.4                 | v1.25                     | Fixed `CVE-2025-23266 <https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2025-23266>`__ and `CVE-2025-23267 <https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2025-23267>`__. |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.27                     |                                                                                                                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 2.0.69                | v1.21                     | Upgraded the base images of the add-on.                                                                                                                                          |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.23                     |                                                                                                                                                                                  |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.25                     |                                                                                                                                                                                  |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.27                     |                                                                                                                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 2.0.48                | v1.21                     | Fixed an issue that occurred during the installation of the driver.                                                                                                              |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.23                     |                                                                                                                                                                                  |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.25                     |                                                                                                                                                                                  |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.27                     |                                                                                                                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 2.0.46                | v1.21                     | -  Supported NVIDIA driver 535.                                                                                                                                                  |
   |                       |                           | -  Non-root users can use xGPUs.                                                                                                                                                 |
   |                       | v1.23                     | -  Optimized startup logic.                                                                                                                                                      |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.25                     |                                                                                                                                                                                  |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.27                     |                                                                                                                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 1.2.28                | v1.19                     | -  Adapted to Ubuntu 22.04.                                                                                                                                                      |
   |                       |                           | -  Optimized the automatic mounting of the GPU driver directory.                                                                                                                 |
   |                       | v1.21                     |                                                                                                                                                                                  |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.23                     |                                                                                                                                                                                  |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.25                     |                                                                                                                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 1.2.20                | v1.19                     | Set the add-on alias to **gpu**.                                                                                                                                                 |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.21                     |                                                                                                                                                                                  |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.23                     |                                                                                                                                                                                  |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.25                     |                                                                                                                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 1.2.15                | v1.15                     | CCE clusters v1.23 are supported.                                                                                                                                                |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.17                     |                                                                                                                                                                                  |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.19                     |                                                                                                                                                                                  |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.21                     |                                                                                                                                                                                  |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.23                     |                                                                                                                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 1.2.9                 | v1.15                     | CCE clusters v1.21 are supported.                                                                                                                                                |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.17                     |                                                                                                                                                                                  |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.19                     |                                                                                                                                                                                  |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.21                     |                                                                                                                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | 1.2.2                 | v1.15                     | Supported the new EulerOS kernel.                                                                                                                                                |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.17                     |                                                                                                                                                                                  |
   |                       |                           |                                                                                                                                                                                  |
   |                       | v1.19                     |                                                                                                                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. |image1| image:: /_static/images/en-us_image_0000002516199377.png
.. |image2| image:: /_static/images/en-us_image_0000002516079369.png
