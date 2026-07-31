:original_name: cce_bulletin_0098.html

.. _cce_bulletin_0098:

EOM of CentOS
=============

**Released**: Oct 23, 2024

CentOS has reached its end of maintenance (EOM) date, which means it will no longer receive updates or support. The CentOS public images on CCE are sourced from the official CentOS. As a result, CCE will no longer provide support for CentOS once it is no longer maintained. This section describes the effects of the CentOS EOM and offers solutions to mitigate them.

Background
----------

On December 8, 2020, CentOS officially announced the plan to stop maintaining CentOS Linux and launched CentOS Stream. For more information, see the `CentOS official documentation <https://blog.centos.org/2020/12/future-is-centos-stream/>`__.

CentOS 8 ended on December 31, 2021, and CentOS 7 ended on June 30, 2024. CentOS 9 and any future versions are no longer available, and there will be no further software patches or updates provided by the CentOS official. CentOS services may be exposed to risks or even become unavailable, and it will not be possible to restore them in a timely manner.

Impact
------

According to the official CentOS change plan, there will be the following impacts on users:

-  CentOS 7 users cannot receive any software maintenance or support, including bug fixes and feature updates, after June 30, 2024.
-  CCE will not remove CentOS 7 public images, and nodes created with CentOS 7 will not be affected, but images will no longer get updated.
-  CCE will provide support for CentOS in line with the official CentOS release date.

Solution
--------

-  **For a newly created node pool or node**

   When creating a node pool or node, change CentOS to a supported OS. HCE is recommended.

-  **For an existing node pool**

   Change CentOS to a supported OS. If you do not need to modify the node configurations, such as the number and type of VPCs and disks, but you want to modify the node OS image and your software is not tightly linked to the original OS, it is recommended that you reset the node to change the OS.

   #. In the navigation pane, choose **Nodes**. On the displayed page, click the **Node Pools** tab.
   #. Select the node pool to be updated, click **Update**, and change CentOS to a supported OS. HCE is recommended.
   #. In the node list of the target node pool, select a node and choose **More** > **Reset Node** in the **Operation** column. (After a node is reset, the node OS will be reinstalled. Before resetting a node, drain the node to gracefully evict the pods running on the node to other available nodes. Perform this operation during off-peak hours.)

   +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+
   | Supported OS          | Description                                                                                                                                                                                                                       | Intended Audience                                                                                                |
   +=======================+===================================================================================================================================================================================================================================+==================================================================================================================+
   | HCE                   | HCE is a cloud OS built on openEuler.                                                                                                                                                                                             | Individuals or enterprises who prefer free images and want to keep using images from the open-source communities |
   |                       |                                                                                                                                                                                                                                   |                                                                                                                  |
   |                       | It offers cloud native, high-performing, secure, and easy-to-migrate capabilities. This accelerates service migration to the cloud and promotes application innovation. You can use it to replace OSs such as CentOS and EulerOS. |                                                                                                                  |
   +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+
   | Ubuntu                | Ubuntu is a Linux distribution. Different OSs reflect different usage habits and application compatibilities.                                                                                                                     | Individuals or enterprises who can handle the cost of switching OSs                                              |
   +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------+

   .. important::

      To upgrade a node's OS, replace the system disks in batches. It is important to avoid storing any important data in the system disks or back up the data beforehand. The upgrade process will not affect the data disks.
