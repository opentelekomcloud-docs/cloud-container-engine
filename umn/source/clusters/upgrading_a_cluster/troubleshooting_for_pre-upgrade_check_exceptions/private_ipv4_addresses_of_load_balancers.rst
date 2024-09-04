:original_name: cce_10_0500.html

.. _cce_10_0500:

Private IPv4 Addresses of Load Balancers
========================================

Check Items
-----------

Check whether the load balancer associated with a Service is allocated with a private IPv4 address.

Solution
--------

**Solution 1**: Delete the Service that is associated with a load balancer without a private IPv4 address.

**Solution 2**: Bind a private IP address to the load balancer without a private IPv4 address. The procedure is as follows:

#. Obtain the load balancer associated with the target Service.

   -  Method 1: Obtain the load balancer ID based on the pre-upgrade check log. Go to the ELB console and filter load balancers by load balancer ID.

      .. code-block::

         elbs (ids: [*****]) without ipv4 private ip, please bind private ip to these elbs and try again

   -  Method 2: Log in to the CCE console and click the cluster name to access the cluster console. Then, choose **Services & Ingresses** in the navigation pane. In the right pane, click the name of the target load balancer to go to the ELB page.

#. Check whether the load balancer has a private IPv4 address.
#. Bind a private IP address to the load balancer without a private IPv4 address.

   a. Log in to the CCE console and click the name of the target load balancer.
   b. On the **Summary** tab, click **Bind** next to **Private IPv4 address**.
   c. Configure the subnet and IPv4 address, and click **OK**.
