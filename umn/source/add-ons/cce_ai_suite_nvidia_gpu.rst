:original_name: cce_10_0141.html

.. _cce_10_0141:

CCE AI Suite (NVIDIA GPU)
=========================

Introduction
------------

NVIDIA GPU is a device management add-on that supports GPUs in containers. To use GPU nodes in a cluster, this add-on must be installed.

Constraints
-----------

-  The driver to be downloaded must be a **.run** file.
-  Only NVIDIA Tesla drivers are supported, not GRID drivers.
-  When installing or reinstalling the add-on, ensure that the driver download address is correct and accessible. CCE does not verify the address validity.
-  The gpu-beta add-on only enables you to download the driver and execute the installation script. The add-on status only indicates that how the add-on is running, not whether the driver is successfully installed.
-  CCE does not guarantee the compatibility between the GPU driver version and the CDUA library version of your application. You need to check the compatibility by yourself.
-  If a custom OS image has had a a GPU driver installed, CCE cannot ensure that the GPU driver is compatible with other GPU components such as the monitoring components used in CCE.

Installing the Add-on
---------------------

#. Log in to the CCE console and click the cluster name to access the cluster console. Choose **Add-ons** in the navigation pane, locate **CCE AI Suite (NVIDIA GPU)** on the right, and click **Install**.
#. Configure the add-on parameters.

   -  **NVIDIA Driver**: Enter the link for downloading the NVIDIA driver. All GPU nodes in the cluster will use this driver.

      .. important::

         -  If the download link is a public network address, for example, **https://us.download.nvidia.com/tesla/470.103.01/NVIDIA-Linux-x86_64-470.103.01.run**, bind an EIP to each GPU node. For details about how to obtain the driver link, see :ref:`Obtaining the Driver Link from Public Network <cce_10_0141__section95451728192112>`.
         -  If the download link is an OBS URL, you do not need to bind an EIP to GPU nodes. For details about how to obtain the driver link, see :ref:`Obtaining the Driver Link from OBS <cce_10_0141__section14922133914508>`.
         -  Ensure that the NVIDIA driver version matches the GPU node.
         -  After the driver version is changed, restart the node for the change to take effect.

   -  **Driver Selection**: If you do not want all GPU nodes in a cluster to use the same driver, CCE allows you to install a different GPU driver for each node pool.

      .. note::

         -  The add-on installs the driver with the version specified by the node pool. The driver takes effect only for new pool nodes.
         -  After the driver version is updated, it takes effect on the nodes newly added to the node pool. Existing nodes must restart to apply the changes.

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
#. Click **Create Node** and select the GPU node to be created in the **Specifications** area. The GPU card model of the node is displayed in the lower part of the page.

3. Visit https://www.nvidia.com/Download/Find.aspx?lang=en.

4. Select the driver information on the **NVIDIA Driver Downloads** page, as shown in :ref:`Figure 1 <cce_10_0141__fig11696366517>`. **Operating System** must be **Linux 64-bit**.

   .. _cce_10_0141__fig11696366517:

   .. figure:: /_static/images/en-us_image_0000001851745764.png
      :alt: **Figure 1** Setting parameters

      **Figure 1** Setting parameters

5. After confirming the driver information, click **SEARCH**. A page is displayed, showing the driver information, as shown in :ref:`Figure 2 <cce_10_0141__fig7873421145213>`. Click **DOWNLOAD**.

   .. _cce_10_0141__fig7873421145213:

   .. figure:: /_static/images/en-us_image_0000001851587044.png
      :alt: **Figure 2** Driver information

      **Figure 2** Driver information

6. Obtain the driver link in either of the following ways:

   -  Method 1: As shown in :ref:`Figure 3 <cce_10_0141__fig5901194614534>`, find *url=/tesla/470.103.01/NVIDIA-Linux-x86_64-470.103.01.run* in the browser address box. Then, supplement it to obtain the driver link https://us.download.nvidia.com/tesla/470.103.01/NVIDIA-Linux-x86_64-470.103.01.run. By using this method, you must bind an EIP to each GPU node.

   -  Method 2: As shown in :ref:`Figure 3 <cce_10_0141__fig5901194614534>`, click **AGREE & DOWNLOAD** to download the driver. Then, upload the driver to OBS and record the OBS URL. By using this method, you do not need to bind an EIP to GPU nodes.

      .. _cce_10_0141__fig5901194614534:

      .. figure:: /_static/images/en-us_image_0000001897906445.png
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

.. table:: **Table 1** Add-on components

   +-------------------------+----------------------------------------------------+---------------+
   | Component               | Description                                        | Resource Type |
   +=========================+====================================================+===============+
   | nvidia-driver-installer | Used for installing an NVIDIA driver on GPU nodes. | DaemonSet     |
   +-------------------------+----------------------------------------------------+---------------+

.. |image1| image:: /_static/images/en-us_image_0000001898025961.png
