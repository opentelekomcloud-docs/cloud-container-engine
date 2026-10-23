:original_name: cce_10_0792.html

.. _cce_10_0792:

Collecting Audit Logs
=====================

CCE allows you to collect the logs of master nodes. On the **Logging** page, choose **Kubernetes Audit Logs** and determine whether to report Kubernetes audit logs to LTS.

Constraints
-----------

-  The cluster version must be v1.21.7-r0 or later, v1.23.5-r0 or later, or 1.25.
-  The LTS resource quota must be sufficient.

Audit Logs
----------

.. table:: **Table 1** Audit logs

   +-----------------------+-----------+---------------------+-------------------------------------------------------------------------------------------------------------------------+
   | Log Type              | Component | Log Stream          | Description                                                                                                             |
   +=======================+===========+=====================+=========================================================================================================================+
   | Kubernetes audit logs | audit     | audit-{{clusterID}} | An audit log is a chronological record of user operations on Kubernetes APIs and control plane activities for security. |
   +-----------------------+-----------+---------------------+-------------------------------------------------------------------------------------------------------------------------+

Enabling Kubernetes Audit Logging for a Cluster
-----------------------------------------------

**Enabling Audit Logging During Cluster Creation**

#. Log in to the CCE console.
#. Click **Create Cluster**. Configure the parameters and click **Next: Select Add-on**.
#. On the displayed page, select **Cloud Native Log Collection** and click **Next: Configure Add-on**.
#. On the displayed page, select **Kubernetes Audit Logs** for **Cloud Native Log Collection**.
#. Click **Next: Confirm Settings**.

**Enabling Audit Logging for an Existing Cluster**

#. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Logging**.

#. Click the **Kubernetes Audit Logs** tab and modify the settings in **Logging Settings**.


   .. figure:: /_static/images/en-us_image_0000002662009903.png
      :alt: **Figure 1** Enabling audit logging for an existing cluster

      **Figure 1** Enabling audit logging for an existing cluster

#. Determine whether to enable logging for the audit component. If yes, click |image1|.

Viewing Kubernetes Audit Logs of a Cluster
------------------------------------------

**Viewing Kubernetes Audit Logs of the Target Cluster on the CCE Console**

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Logging**.
#. Click the **Kubernetes Audit Logs** tab to view the audit logs of the cluster. For details about related operations, see `LTS User Guide <https://docs.otc.t-systems.com/en-us/usermanual/lts/lts_04_1053.html>`__.

**Viewing Kubernetes Audit Logs of the Target Cluster on the LTS Console**

#. Log in to the LTS console and choose **Log Management**.
#. Search for the log group by cluster ID and click the log group name to view the log streams. For details, see `Log Tank Service User Guide <https://docs.otc.t-systems.com/en-us/usermanual/lts/lts_04_1053.html>`__.

Disabling Kubernetes Audit Logging
----------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Logging**.
#. Click the **Kubernetes Audit Logs** tab.
#. Disable the audit component and click |image2|.

   .. note::

      After you disable audit logging, logs are no longer written to the original log stream, but the existing logs will not be deleted and expenditures may be incurred for this.

.. |image1| image:: /_static/images/en-us_image_0000002631530770.png
.. |image2| image:: /_static/images/en-us_image_0000002631530774.png
