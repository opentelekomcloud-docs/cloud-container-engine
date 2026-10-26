:original_name: cce_faq_00039.html

.. _cce_faq_00039:

How Do I Locate the Fault When a Cluster Is Unavailable?
========================================================

This section shows you how to locate the fault when a cluster becomes unavailable.

Troubleshooting
---------------

Possible causes are listed in order of likelihood.

Check these causes one by one until you find the cause of the fault.

-  :ref:`Check Item 1: Security Group Modification <cce_faq_00039__section48059154014>`
-  :ref:`Check Item 2: Cluster Overloaded <cce_faq_00039__section1821659134014>`

If the fault persists, contact the customer service to help you locate the fault.

.. _cce_faq_00039__section48059154014:

Check Item 1: Security Group Modification
-----------------------------------------

#. Log in to the management console and choose **Service List** > **Networking** > **Virtual Private Cloud**. In the navigation pane, choose **Access Control** > **Security Groups** and find the security group of the master node in the cluster.

   The name of this security group is in the format of *{cluster_name}*-cce-**control**\ ``-``\ *{ID}*.

#. Click the security group. On the details page displayed, ensure that the security group rules of the master node are correct.

   For details, see :ref:`How Do I Modify Cluster Security Group Rules? <cce_faq_00265>`

.. _cce_faq_00039__section1821659134014:

Check Item 2: Cluster Overloaded
--------------------------------

**Symptom**

The resource usage on the master nodes in the cluster reaches 100%.

**Possible Cause**

When a cluster has a large number of resources created simultaneously, it causes an overload on the API server. This, in turn, overloads the master nodes and leads to OOM issues.

**Solution**

Increase the cluster management scale. A larger cluster management scale means higher capacity and improved performance of the master nodes. For details, see :ref:`Changing a Cluster Scale <cce_10_0403>`.

If a cluster is overloaded, you can submit a service ticket for technical support.
