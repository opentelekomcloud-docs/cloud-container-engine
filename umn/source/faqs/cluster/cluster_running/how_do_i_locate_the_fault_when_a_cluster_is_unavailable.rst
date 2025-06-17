:original_name: cce_faq_00039.html

.. _cce_faq_00039:

How Do I Locate the Fault When a Cluster Is Unavailable?
========================================================

This section provides you with some operations to locate the fault when a cluster becomes unavailable.

Troubleshooting
---------------

The issues here are described in order of how likely they are to occur.

Check these causes one by one until you find the cause of the fault.

-  :ref:`Check Item 1: Whether the Security Group Is Modified <cce_faq_00039__section48059154014>`
-  :ref:`Check Item 2: Whether the Cluster Is Overloaded <cce_faq_00039__section1821659134014>`

If the fault persists, contact the customer service to help you locate the fault.

.. _cce_faq_00039__section48059154014:

Check Item 1: Whether the Security Group Is Modified
----------------------------------------------------

#. Log in to the management console and choose **Service List** > **Networking** > **Virtual Private Cloud**. In the navigation pane, choose **Access Control** > **Security Groups** and find the security group of the master node in the cluster.

   The name of this security group is in the format of *Cluster name*-cce-**control**\ ``-``\ *ID*.

#. Click the security group. On the details page displayed, ensure that the security group rules of the master node are correct.

   For details, see :ref:`How Can I Configure a Security Group Rule for a Cluster? <cce_faq_00265>`

.. _cce_faq_00039__section1821659134014:

Check Item 2: Whether the Cluster Is Overloaded
-----------------------------------------------

**Symptom**

The resource usage on the master nodes in the cluster reaches 100%.

**Possible cause**

When a cluster has a large number of resources created simultaneously, it causes an overload on the API server. This, in turn, overloads the master nodes and leads to OOM issues.

**Solution**

Increase the cluster management scale. A larger cluster management scale means higher capacity and improved performance of the master nodes. For details, see :ref:`Changing Cluster Scale <cce_10_0403>`.

If a cluster is overloaded, you can submit a service ticket for technical support.
