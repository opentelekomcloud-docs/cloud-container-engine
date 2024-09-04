:original_name: cce_faq_00443.html

.. _cce_faq_00443:

How Do I Modify ECS Configurations When an ECS Cannot Be Managed by a Node Pool?
================================================================================

If an ECS cannot be managed by a node pool due to the reasons listed in this section, you can modify the configuration to manage the ECS.

+-----------------------------------+----------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+
| Cause                             | Solution                                                                               | Reference                                                                              |
+===================================+========================================================================================+========================================================================================+
| Inconsistent flavors              | Change the ECS flavor to that contained in the node pool.                              | :ref:`Modifying the Flavor of an ECS <cce_faq_00443__section61147281444>`              |
+-----------------------------------+----------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+
| Different data disk configuration | Change the data disk configuration of the ECS to be the same as that of the node pool. | :ref:`Changing Data Disk Configuration of an ECS <cce_faq_00443__section132294014488>` |
+-----------------------------------+----------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+
| Different ECS groups              | Change the ECS group of the ECS to be the same as that of the node pool.               | :ref:`Change the ECS Group of an ECS <cce_faq_00443__section19769166194919>`           |
+-----------------------------------+----------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------+

.. _cce_faq_00443__section61147281444:

Modifying the Flavor of an ECS
------------------------------

.. note::

   The flavor of the ECS to be managed must be changed to that contained in the target node pool.

#. Log in to the ECS console.
#. Click the name of the target ECS. On the page displayed, click **Stop** in the upper right corner. After the ECS is stopped, choose **More** > **Modify Specifications** in the **Operation** column.
#. On the **Modify ECS Specifications** page, select the needed flavor and submit the application.
#. Go back to the ECS list page and start the ECS.

.. _cce_faq_00443__section132294014488:

Changing Data Disk Configuration of an ECS
------------------------------------------

.. note::

   The number, space, and type of data disks of the ECS to be managed must be the same as those of data disks in the node pool.

**Data Disk Number**

#. Log in to the ECS console.
#. Click the name of the target ECS to access the ECS details page.
#. Click the **Disks** tab.

   -  If there are fewer data disks on the node to be managed than the number of data disks configured for the target node pool, you need to add more disks.

   -  If there are more data disks on the node to be managed than the number of data disks configured for the target node pool, you need to remove some disks.

      Click **Detach** on the right of the EVS disk to be removed.

**Data Disk Space**

#. Log in to the ECS console.
#. Click the name of the target ECS to access the ECS details page.
#. Click the **Disks** tab and click **Expand Capacity** on the right of the EVS disk to be expanded.
#. Configure **New Capacity** following instructions.
#. Click **Next** and submit the order following instructions.

**Data Disk Type**

#. Log in to the ECS console.
#. Click the name of the target ECS to access the ECS details page.
#. Click the **Disks** tab and click **Modify Specifications** on the right of the right of the EVS disk to be expanded.
#. Configure **Disk Type** following instructions.
#. Click **Submit**.

.. _cce_faq_00443__section19769166194919:

Change the ECS Group of an ECS
------------------------------

.. note::

   The ECS group of the ECS to be managed must be the same as that of the target node pool.

#. Log in to the ECS console.
#. In the navigation pane, choose **Elastic Cloud Server** > **ECS Group**.
#. Locate the row containing the target ECS group and click **Add ECS** in the **Operation** column.
#. In the dialog box displayed, select the ECS to be added.
#. Click **OK** to add the ECS to the ECS group.
