:original_name: cce_faq_00039.html

.. _cce_faq_00039:

How Do I Rectify the Fault When the Cluster Status Is Unavailable?
==================================================================

If the cluster is **Unavailable**, perform the following operations to rectify the fault:

Fault Locating
--------------

Troubleshooting methods are sorted based on the occurrence probability of the possible causes. You are advised to check the possible causes from high probability to low probability to quickly locate the cause of the problem.

If the fault persists after a possible cause is rectified, check other possible causes.

-  :ref:`Check Item 1: Whether the Security Group Is Modified <cce_faq_00039__section48059154014>`
-  :ref:`Check Item 2: Whether There Are Residual Listeners and Backend Server Groups on the Load Balancer <cce_faq_00039__section1821659134014>`


.. figure:: /_static/images/en-us_image_0000001704574345.png
   :alt: **Figure 1** Fault locating

   **Figure 1** Fault locating

.. _cce_faq_00039__section48059154014:

Check Item 1: Whether the Security Group Is Modified
----------------------------------------------------

#. Log in to the management console, and choose **Service List** > **Networking** > **Virtual Private Cloud**. In the navigation pane on the left, choose **Access Control** > **Security Groups** to find the security group of the master node in the cluster.

   The name of this security group is in the format of *Cluster name*-cce-**control**\ ``-``\ *ID*.

#. Click the security group. On the details page displayed, ensure that the security group rules of the master node are correct.

   For details about security, see :ref:`Configuring Cluster Security Group Rules <cce_faq_00265>`.

.. _cce_faq_00039__section1821659134014:

Check Item 2: Whether There Are Residual Listeners and Backend Server Groups on the Load Balancer
-------------------------------------------------------------------------------------------------

**Reproducing the Problem**

A cluster exception occurs when a LoadBalancer Service is being created or deleted. After the fault is rectified, the Service is deleted successfully, but there are residual listeners and backend server group.

#. Pre-create a CCE cluster. In the cluster, use the official Nginx image to create workloads, preset load balancers, Services, and ingresses.
#. Ensure that the cluster is running properly and the Nginx workload is stable.
#. Create and delete 10 LoadBalancer Services every 20 seconds.
#. An injection exception occurs in the cluster. For example, the etcd pod is unavailable or the cluster is hibernated.

**Possible Cause**

There are residual listeners and backend server groups on the load balancer.

**Solution**

Manually clear residual listeners and backend server groups.

#. Log in to the management console and choose **Network** > **Elastic Load Balance** from the service list.
#. In the load balancer list, click the name of the target load balancer to go to the details page. On the **Listeners** tab page, locate the target listener and delete it.
#. On the **Backend Server Groups** tab page, locate the target backend server group and delete it.
