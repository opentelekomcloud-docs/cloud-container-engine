:original_name: cce_10_0141.html

.. _cce_10_0141:

CCE AI Suite (NVIDIA GPU)
=========================

Introduction
------------

NVIDIA GPU is a device management add-on that supports GPUs in containers. To use GPU nodes in a cluster, this add-on must be installed.

Notes and Constraints
---------------------

-  The driver to be downloaded must be a **.run** file.
-  Only NVIDIA Tesla drivers are supported, not GRID drivers.
-  When installing or reinstalling the add-on, ensure that the driver download address is correct and accessible. CCE does not verify the address validity.
-  The gpu-beta add-on only enables you to download the driver and execute the installation script. The add-on status only indicates that how the add-on is running, not whether the driver is successfully installed.
-  CCE does not guarantee the compatibility between the GPU driver version and the CUDA library version of your application. You need to check the compatibility by yourself.
-  If a custom OS image has had a GPU driver installed, CCE cannot ensure that the GPU driver is compatible with other GPU components such as the monitoring components used in CCE.

Installing the Add-on
---------------------

#. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Add-ons**, locate **CCE AI Suite (NVIDIA GPU)** on the right, and click **Install**.
#. Determine whether to enable **Use DCGM-Exporter to Observe DCGM Metrics**. After this function is enabled, DCGM-Exporter is deployed on the GPU node.

   .. important::

      If the add-on version is 2.7.40 or later, DCGM-Exporter can be deployed. DCGM-Exporter maintains the community capability and does not support the sharing mode or GPU virtualization.

#. Configure the add-on parameters.

   .. table:: **Table 1** Add-on parameters

      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                |
      +===================================+============================================================================================================================================================================================================================================================================================================================================+
      | Default Cluster Driver            | All GPU nodes in a cluster use the same driver. You can select a proper GPU driver version or customize the driver link and enter the download link of the NVIDIA driver.                                                                                                                                                                  |
      |                                   |                                                                                                                                                                                                                                                                                                                                            |
      |                                   | .. important::                                                                                                                                                                                                                                                                                                                             |
      |                                   |                                                                                                                                                                                                                                                                                                                                            |
      |                                   |    NOTICE:                                                                                                                                                                                                                                                                                                                                 |
      |                                   |                                                                                                                                                                                                                                                                                                                                            |
      |                                   |    -  If the download link is a public network address, for example, **https://us.download.nvidia.com/tesla/470.103.01/NVIDIA-Linux-x86_64-470.103.01.run**, bind an EIP to each GPU node. For details about how to obtain the driver link, see :ref:`Obtaining the Driver Link from Public Network <cce_10_0141__section95451728192112>`. |
      |                                   |    -  If the download link is an OBS URL, you do not need to bind an EIP to GPU nodes. For details about how to obtain the driver link, see :ref:`Obtaining the Driver Link from OBS <cce_10_0141__section14922133914508>`.                                                                                                                |
      |                                   |    -  Ensure that the NVIDIA driver version matches the GPU node.                                                                                                                                                                                                                                                                          |
      |                                   |    -  If the driver version is changed, restart the node to apply the change.                                                                                                                                                                                                                                                              |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. note::

      After the add-on is installed, you can configure GPU virtualization and node pool drivers on the **Heterogeneous Resources** tab in **Settings**.

#. Click **Install**.

   .. note::

      If the add-on is uninstalled, GPU pods newly scheduled to the nodes cannot run properly, but GPU pods already running on the nodes will not be affected.

Verifying the Add-on
--------------------

After the add-on is installed, run the **nvidia-smi** command on the GPU node and the container that schedules GPU resources to verify the availability of the GPU device and driver.

-  GPU node:

   .. code-block::

      # If the add-on version is earlier than 2.0.0, run the following command:
      cd /opt/cloud/cce/nvidia/bin && ./nvidia-smi

      # If the add-on version is 2.0.0 or later and the driver installation path is changed, run the following command:
      cd /usr/local/nvidia/bin && ./nvidia-smi

-  Container:

   .. code-block::

      cd /usr/local/nvidia/bin && ./nvidia-smi

If GPU information is returned, the device is available and the add-on has been installed.

|image1|

.. _cce_10_0141__section95451728192112:

Obtaining the Driver Link from Public Network
---------------------------------------------

#. Log in to the CCE console.
#. Create a node. In the **Specifications** area, select the GPU node flavor. The GPU card models are displayed in the lower part of the area.

3. Log in to the `NVIDIA driver download page <https://www.nvidia.com/en-us/drivers/>`__ and search for the driver information. The OS must be **Linux 64-bit**.


   .. figure:: /_static/images/en-us_image_0000002218820674.png
      :alt: **Figure 1** Selecting parameters

      **Figure 1** Selecting parameters

4. After confirming the driver information, click **Find**. On the displayed page, find the driver to be downloaded and click **View**.


   .. figure:: /_static/images/en-us_image_0000002218820658.png
      :alt: **Figure 2** Viewing the driver information

      **Figure 2** Viewing the driver information

5. Click **Download** and copy the download link.


   .. figure:: /_static/images/en-us_image_0000002253620537.png
      :alt: **Figure 3** Obtaining the link

      **Figure 3** Obtaining the link

.. _cce_10_0141__section14922133914508:

Obtaining the Driver Link from OBS
----------------------------------

#. Upload the driver to OBS and set the driver file to public read.

   .. note::

      When the node is restarted, the driver will be downloaded and installed again. Ensure that the OBS bucket link of the driver is valid.

#. In the bucket list, click a bucket name, and then the **Overview** page of the bucket is displayed.
#. In the navigation pane, choose **Objects**.
#. Select the name of the target object and copy the driver link on the object details page.

Components
----------

.. table:: **Table 2** Add-on components

   +--------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
   | Component                | Description                                                                                                                                                                      | Resource Type |
   +==========================+==================================================================================================================================================================================+===============+
   | nvidia-driver-installer  | A workload for installing the NVIDIA GPU driver on a node, which only uses resources during the installation process (Once the installation is finished, no resources are used.) | DaemonSet     |
   +--------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
   | nvidia-gpu-device-plugin | A Kubernetes device plugin that provides NVIDIA GPU heterogeneous compute for containers                                                                                         | DaemonSet     |
   +--------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+
   | nvidia-operator          | A component that provides NVIDIA GPU node management capabilities for clusters                                                                                                   | Deployment    |
   +--------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------+

GPU Metrics
-----------

For more details, see :ref:`GPU Metrics <cce_10_0955>`.

Change History
--------------

.. table:: **Table 3** Release history

   +-----------------------+---------------------------+----------------------------------------------------------------------------------+
   | Add-on Version        | Supported Cluster Version | New Feature                                                                      |
   +=======================+===========================+==================================================================================+
   | 2.7.63                | v1.28                     | Fixed the security vulnerabilities.                                              |
   |                       |                           |                                                                                  |
   |                       | v1.29                     |                                                                                  |
   |                       |                           |                                                                                  |
   |                       | v1.30                     |                                                                                  |
   |                       |                           |                                                                                  |
   |                       | v1.31                     |                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------+
   | 2.7.19                | v1.28                     | Fixed the nvidia-container-toolkit CVE-2024-0132 container escape vulnerability. |
   |                       |                           |                                                                                  |
   |                       | v1.29                     |                                                                                  |
   |                       |                           |                                                                                  |
   |                       | v1.30                     |                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------+
   | 2.7.13                | v1.28                     | -  Supported xGPU configuration by node pool.                                    |
   |                       |                           | -  Supported GPU rendering.                                                      |
   |                       | v1.29                     | -  Clusters v1.30 are supported.                                                 |
   |                       |                           |                                                                                  |
   |                       | v1.30                     |                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------+
   | 2.6.4                 | v1.28                     | Updated the isolation logic of GPU cards.                                        |
   |                       |                           |                                                                                  |
   |                       | v1.29                     |                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------+
   | 2.6.1                 | v1.28                     | Upgraded the base images of the add-on.                                          |
   |                       |                           |                                                                                  |
   |                       | v1.29                     |                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------+
   | 2.5.6                 | v1.28                     | Fixed an issue that occurred during the installation of the driver.              |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------+
   | 2.5.4                 | v1.28                     | Clusters v1.28 are supported.                                                    |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------+
   | 2.0.69                | v1.21                     | Upgraded the base images of the add-on.                                          |
   |                       |                           |                                                                                  |
   |                       | v1.23                     |                                                                                  |
   |                       |                           |                                                                                  |
   |                       | v1.25                     |                                                                                  |
   |                       |                           |                                                                                  |
   |                       | v1.27                     |                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------+
   | 2.0.48                | v1.21                     | Fixed an issue that occurred during the installation of the driver.              |
   |                       |                           |                                                                                  |
   |                       | v1.23                     |                                                                                  |
   |                       |                           |                                                                                  |
   |                       | v1.25                     |                                                                                  |
   |                       |                           |                                                                                  |
   |                       | v1.27                     |                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------+
   | 2.0.46                | v1.21                     | -  Supported Nvidia driver 535.                                                  |
   |                       |                           | -  Non-root users can use xGPUs.                                                 |
   |                       | v1.23                     | -  Optimized startup logic.                                                      |
   |                       |                           |                                                                                  |
   |                       | v1.25                     |                                                                                  |
   |                       |                           |                                                                                  |
   |                       | v1.27                     |                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------+
   | 1.2.28                | v1.19                     | -  Adapted to Ubuntu 22.04.                                                      |
   |                       |                           | -  Optimized the automatic mounting of the GPU driver directory.                 |
   |                       | v1.21                     |                                                                                  |
   |                       |                           |                                                                                  |
   |                       | v1.23                     |                                                                                  |
   |                       |                           |                                                                                  |
   |                       | v1.25                     |                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------+
   | 1.2.20                | v1.19                     | Set the add-on alias to **gpu**.                                                 |
   |                       |                           |                                                                                  |
   |                       | v1.21                     |                                                                                  |
   |                       |                           |                                                                                  |
   |                       | v1.23                     |                                                                                  |
   |                       |                           |                                                                                  |
   |                       | v1.25                     |                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------+
   | 1.2.15                | v1.15                     | CCE clusters v1.23 are supported.                                                |
   |                       |                           |                                                                                  |
   |                       | v1.17                     |                                                                                  |
   |                       |                           |                                                                                  |
   |                       | v1.19                     |                                                                                  |
   |                       |                           |                                                                                  |
   |                       | v1.21                     |                                                                                  |
   |                       |                           |                                                                                  |
   |                       | v1.23                     |                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------+
   | 1.2.9                 | v1.15                     | CCE clusters v1.21 are supported.                                                |
   |                       |                           |                                                                                  |
   |                       | v1.17                     |                                                                                  |
   |                       |                           |                                                                                  |
   |                       | v1.19                     |                                                                                  |
   |                       |                           |                                                                                  |
   |                       | v1.21                     |                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------+
   | 1.2.2                 | v1.15                     | Supported the new EulerOS kernel.                                                |
   |                       |                           |                                                                                  |
   |                       | v1.17                     |                                                                                  |
   |                       |                           |                                                                                  |
   |                       | v1.19                     |                                                                                  |
   +-----------------------+---------------------------+----------------------------------------------------------------------------------+

.. |image1| image:: /_static/images/en-us_image_0000002253620561.png
