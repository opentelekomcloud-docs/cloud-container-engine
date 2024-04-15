:original_name: cce_faq_00111.html

.. _cce_faq_00111:

Why Cannot I Create a CCE Cluster?
==================================

Overview
--------

This section describes how to locate and rectify the fault if you fail to create a CCE cluster.

Details
-------

**Possible causes:**

#. The Network Time Protocol daemon (ntpd) is not installed or fails to be installed, Kubernetes components fail to pass the pre-verification, or the disk partition is incorrect. The current solution is to create a cluster again. For details about how to locate the fault, see :ref:`Locating the Failure Cause <cce_faq_00111__section398812378182>`.

.. _cce_faq_00111__section398812378182:

Locating the Failure Cause
--------------------------

View the cluster logs to identify the cause and rectify the fault.

#. Log in to the CCE console. In the navigation pane, click **Operation Records** above the cluster list to view operation records.
#. Click the record of the **Failed** status to view error information.
#. Rectify the fault based on the error information and create a cluster again.
