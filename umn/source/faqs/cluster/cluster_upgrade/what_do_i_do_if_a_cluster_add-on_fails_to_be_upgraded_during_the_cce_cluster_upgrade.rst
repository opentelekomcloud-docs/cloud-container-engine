:original_name: cce_faq_00402.html

.. _cce_faq_00402:

What Do I Do If a Cluster Add-On Fails to be Upgraded During the CCE Cluster Upgrade?
=====================================================================================

Overview
--------

This section describes how to locate and rectify the fault if you fail to upgrade an add-on during the CCE cluster upgrade.

Procedure
---------

#. If the add-on fails to be upgraded, try again first. If the retry fails, perform the following steps to rectify the fault.
#. If a failure message is displayed on the upgrade page, go to the **Add-ons** page to view the add-on status. For an abnormal add-on, click the add-on name to view details.
#. On the pod details page, click **View Events** in the **Operation** column of the abnormal pod.
#. Rectify the fault based on the exception information. For example, delete the pod that is not started or restart it.
#. After the processing is successful, the add-on status changes to **Running**. Ensure that all add-ons are in the **Running** status.
#. Go to the cluster upgrade page and click **Retry**.
