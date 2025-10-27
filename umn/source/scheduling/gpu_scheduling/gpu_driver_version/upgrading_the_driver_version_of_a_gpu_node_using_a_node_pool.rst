:original_name: cce_10_0849.html

.. _cce_10_0849:

Upgrading the Driver Version of a GPU Node Using a Node Pool
============================================================

To ensure proper functioning of GPU nodes, upgrade the NVIDIA driver version if it does not match the CUDA library you use. It is recommended that you use node pools to effectively manage node NVIDIA driver versions. This allows you to schedule applications to a specific node pool with a designated driver version. Additionally, when upgrading drivers, you can perform the upgrade in batches by node pool.

.. note::

   When upgrading the NVIDIA driver of a node by node pool, note that the driver will be reinstalled during the node restart. To prevent any issues, ensure that there are no running tasks on the node before upgrading the driver.

Step 1: Specify the Driver Version of a Node Pool
-------------------------------------------------

#. Log in to the target node and check its driver version. The command for obtaining a driver version varies based on the CCE AI Suite (NVIDIA GPU) version.

   -  For v1.x.x, run the following command:

      .. code-block::

         /opt/cloud/cce/nvidia/bin/nvidia-smi

   -  For v2.0.0 through v2.5.3, run the following command:

      .. code-block::

         /usr/local/nvidia/bin/nvidia-smi

   -  For versions later than v2.5.4, run the following command:

      .. code-block::

         nvidia-smi

   The following command output indicates that the driver version is 510.47.03.

   |image1|

#. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Settings**.

#. Click the **Heterogeneous Resources** tab page. In the **Node Pool Configurations** pane, select the target node pool and driver, or enter the link to the custom driver.

   In this section, the driver will be upgraded to 535.54.03.

#. Click **Confirm Configuration**.

Step 2: Restart the Nodes in the Node Pool
------------------------------------------

.. note::

   Evict pods on a node before restarting the node. For details, see :ref:`Draining a Node <cce_10_0605>`. Make sure to reserve GPU resources to avoid a pod scheduling failure during node drainage. Insufficient resources can affect service running.

#. Log in to the CCE console and click the cluster name to access the cluster console. The **Overview** page is displayed.
#. Choose **Nodes**, locate the target node pool, and click **View Node**.
#. Click the node name and navigate to the ECS page.
#. In the upper right corner, click **Restart**.

Step 3: Verify the Driver Upgrade
---------------------------------

#. Wait a few minutes after restarting the node for the driver to install.

#. Log in to the node and check whether the driver on the node has been updated.

   The command for obtaining a driver varies depending on the CCE AI Suite (NVIDIA GPU) version.

   -  For v1.x.x, run the following command:

      .. code-block::

         /opt/cloud/cce/nvidia/bin/nvidia-smi

   -  For v2.0.0 through v2.5.3, run the following command:

      .. code-block::

         /usr/local/nvidia/bin/nvidia-smi

   -  For versions later than v2.5.4, run the following command:

      .. code-block::

         nvidia-smi

   The following command output indicates that the driver version is 510.47.03.

   |image2|

#. Confirm that the node and its services are running correctly. Then, perform the same operations on the remaining nodes in the node pool individually.

.. |image1| image:: /_static/images/en-us_image_0000002434080480.png
.. |image2| image:: /_static/images/en-us_image_0000002467718941.png
