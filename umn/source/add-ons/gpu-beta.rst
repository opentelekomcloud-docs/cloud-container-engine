:original_name: cce_01_0141.html

.. _cce_01_0141:

gpu-beta
========

Introduction
------------

gpu-beta is a device management add-on that supports GPUs in containers. It supports only NVIDIA Tesla drivers.

Notes and Constraints
---------------------

-  This add-on is available only in certain regions.
-  This add-on can be installed only in CCE clusters of v1.11 or later.
-  If GPU nodes are used in the cluster, the gpu-beta add-on must be installed.
-  The driver to be downloaded must be a **.run** file.
-  Only Tesla drivers are supported, not GRID drivers.

.. important::

   -  If the download link is a public network address, for example, **https://us.download.nvidia.com/tesla/396.37/NVIDIA-Linux-x86_64-396.37.run**, bind an EIP to each GPU node. For details about how to obtain the driver link, see :ref:`Obtaining the Driver Link from Public Network <cce_01_0141__section95451728192112>`.
   -  If the download link is an OBS URL, you do not need to bind an EIP to GPU nodes.
   -  Ensure that the NVIDIA driver version matches the GPU node.
   -  After the driver version is changed, restart the node for the change to take effect.

Installing the Add-on
---------------------

#. Log in to the CCE console. In the navigation pane, choose **Add-ons**. On the **Add-on Marketplace** tab page, click **Install Add-on** under **gpu-beta**.

#. On the **Install Add-on** page, select the cluster and the add-on version, and click **Next: Configuration**.

#. In the **Configuration** step, enter the link to download the NVIDIA driver.

#. Click **Install**.

   After the add-on is installed, click **Go Back to Previous Page**. On the **Add-on Instance** tab page, select the corresponding cluster to view the running instance. This indicates that the add-on has been installed on each GPU node in the cluster.

Verifying the Add-on
--------------------

After the add-on is installed, run the **nvidia-smi** command on the GPU node and the container that schedules GPU resources to verify the availability of the GPU device and driver.

GPU node:

.. code-block::

   cd /opt/cloud/cce/nvidia/bin && ./nvidia-smi

Container:

.. code-block::

   cd /usr/local/nvidia/bin && ./nvidia-smi

If GPU information is returned, the device is available and the add-on is successfully installed.

|image1|

.. _cce_01_0141__section95451728192112:

Obtaining the Driver Link from Public Network
---------------------------------------------

#. Log in to the CCE console.
#. Click **Create Node** and select the GPU node to be created in the **Specifications** area. The GPU card model of the node is displayed in the lower part of the page.

3. Visit https://www.nvidia.com/Download/Find.aspx?lang=en.

4. Select the driver information on the **NVIDIA Driver Downloads** page, as shown in :ref:`Figure 1 <cce_01_0141__fig11696366517>`. **Operating System** must be **Linux 64-bit**.

   .. _cce_01_0141__fig11696366517:

   .. figure:: /_static/images/en-us_image_0000001280466745.png
      :alt: **Figure 1** Setting parameters

      **Figure 1** Setting parameters

5. After confirming the driver information, click **SEARCH**. A page is displayed, showing the driver information, as shown in :ref:`Figure 2 <cce_01_0141__fig7873421145213>`. Click **DOWNLOAD**.

   .. _cce_01_0141__fig7873421145213:

   .. figure:: /_static/images/en-us_image_0181616313.png
      :alt: **Figure 2** Driver information

      **Figure 2** Driver information

6. Obtain the driver link in either of the following ways:

   -  Method 1: As shown in :ref:`Figure 3 <cce_01_0141__fig5901194614534>`, find *url=/tesla/396.37/NVIDIA-Linux-x86_64-396.37.run* in the browser address box. Then, supplement it to obtain the driver link https://us.download.nvidia.com/tesla/396.37/NVIDIA-Linux-x86_64-396.37.run. By using this method, you must bind an EIP to each GPU node.

   -  Method 2: As shown in :ref:`Figure 3 <cce_01_0141__fig5901194614534>`, click **AGREE & DOWNLOAD** to download the driver. Then, upload the driver to OBS and record the OBS URL. By using this method, you do not need to bind an EIP to GPU nodes.

      .. _cce_01_0141__fig5901194614534:

      .. figure:: /_static/images/en-us_image_0181616314.png
         :alt: **Figure 3** Obtaining the link

         **Figure 3** Obtaining the link

Uninstalling the Add-on
-----------------------

#. Log in to the CCE console. In the navigation pane, choose **Add-ons**. On the **Add-on Instance** tab page, select the cluster and click **Uninstall** under **gpu-beta**.
#. In the dialog box displayed, click **Yes** to uninstall the add-on.

   .. note::

      The driver will not be uninstalled during gpu-beta add-on uninstall. If the driver is reinstalled, you must restart all GPU nodes.

.. |image1| image:: /_static/images/en-us_image_0000001238225460.png
