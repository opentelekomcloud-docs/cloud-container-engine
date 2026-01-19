:original_name: cce_faq_00027.html

.. _cce_faq_00027:

How Do I Troubleshoot Problems Occurred When Adding Nodes to a CCE Cluster?
===========================================================================

Notes
-----

-  The node images in the same cluster must be the same. Pay attention to this when creating, adding, or accepting nodes in a cluster.
-  If you need to allocate user space from the data disk when creating a node, do not set the data storage path to any key directory. For example, to store data in the **/home** directory, set the directory to **/home/test** instead of **/home**.

   .. note::

      Do not set **Path inside a node** to the root directory **/**. Otherwise, the mounting fails. Set **Path inside a node** to any of the following:

      -  /opt/xxxx (excluding **/opt/cloud**)
      -  /mnt/xxxx (excluding **/mnt/paas**)
      -  /tmp/xxx
      -  /var/xxx (excluding key directories such as **/var/lib**, **/var/script**, and **/var/paas**)
      -  /xxxx (It cannot conflict with the system directory, such as **bin**, **lib**, **home**, **root**, **boot**, **dev**, **etc**, **lost+found**, **mnt**, **proc**, **sbin**, **srv**, **tmp**, **var**, **media**, **opt**, **selinux**, **sys**, and **usr**.)

      Do not set it to **/home/paas**, **/var/paas**, **/var/lib**, **/var/script**, **/mnt/paas**, or **/opt/cloud**. Otherwise, the system or node installation will fail.

Check Item 1: Available IP Addresses in the Subnet
--------------------------------------------------

**Symptom**

New nodes cannot be added to a CCE cluster, and a message is displayed, indicating that the available IP addresses in the subnet are insufficient.

**Cause Analysis**

The default node subnet CIDR block of the cluster is too small, causing all the available private IP addresses in the subnet to be exhausted. Consequently, it is not possible to allocate any private IP addresses to the nodes.

**Solution**

-  **Scenario 1**: **IP addresses in the VPC CIDR block are not used up.**

   When creating a node, you can select a new node subnet in the network configuration. If no node subnet is available, you can go to the VPC console and create a node subnet.

-  **Scenario 2: All IP addresses in the VPC CIDR block have been used up.**

   If all IP addresses in the VPC CIDR block have been used up, you need to expand the VPC CIDR block and create a node subnet.

   #. Log in to the management console. In the service list, choose **Virtual Private Cloud**. In the VPC list, locate the row containing the target VPC and click **Edit CIDR block** in the **Operation** column.

   #. After adding a secondary CIDR block, click **OK**.

   #. In the navigation pane, choose **Subnets** and click **Create Subnet** to create a subnet for the VPC where the cluster resides.

   #. Go back to the page for adding a node on the CCE console and select the newly created subnet.

      .. note::

         a. Adding subnets to the VPC does not affect the use of the existing CIDR blocks. If the service requirements still cannot be met, you can add more subnets.
         b. Subnets in the same VPC can communicate with each other through the private network.

Check Item 2: EIP Quota
-----------------------

**Symptom**

When a node is added, **EIP** is set to **Auto create**. The node cannot be created, and a message indicating that EIPs are insufficient is displayed.

**Solution**

Two methods are available to solve the problem.

-  **Method 1:** Unbind the VMs bound with EIPs and add a node again.

   #. Log in to the management console.
   #. Choose **Service List** > **Compute** > **Elastic Cloud Server**.
   #. In the ECS list, locate the target ECS and click its name.
   #. On the page displayed, click the **EIPs** tab. In the EIP list, locate the row containing the target EIP, click **Unbind**, and then click **Yes**.
   #. Return to the page for adding a node on the CCE console, select **Use existing** for **EIP**, and add the node again.

-  **Method 2:** Increase the EIP quota.

Check Item 3: Security Group
----------------------------

**Symptom**

A node cannot be added to a CCE cluster.

**Solution**

Click the cluster name to access the **Overview** page. In the **Networking Configuration** area, click the icon next to the value of **Default Node Security Group** to check whether the default security group is deleted and whether the security group rules comply with :ref:`How Can I Configure a Security Group Rule for a Cluster? <cce_faq_00265>`

If your account has multiple clusters and you need to manage network security policies of nodes centrally, you can specify custom security groups.

Check Item 4: Resource Quota
----------------------------

**Symptom**

When a node is added to a CCE cluster, a message is displayed, indicating that the resource quota is insufficient.

**Solution**

You can click **Increase Quota** to go to the corresponding page and increase the quota.

Check Item 5: Private DNS Resolution
------------------------------------

During node creation, software packages are downloaded from OBS via the domain name. A private DNS server must be used to resolve the OBS domain name. Therefore, the DNS server address of the subnet where the node resides must be set to a private DNS server address so that the node can access the private DNS server. By default, the private DNS server is used when you create a subnet. However, if you have modified the subnet DNS, verify that **the DNS server in use can resolve the OBS domain name**. If it cannot, you will need to use the private DNS server.

To confirm and modify the DNS of the subnet where the node resides, take the following steps:

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. View the node subnet.

   -  For the default node pool: In the navigation pane, choose **Overview**. In the **Networking Configuration** area, view the subnet name.

      |image1|

   -  For a custom node pool: In the navigation pane, choose **Node Pools**. On the **Node Pools** tab, click the node pool name and view the subnet name. A node pool may be associated with multiple subnets.

#. Confirm and modify the DNS of the node subnet. If there are multiple node subnets, confirm and modify them one by one.

   a. Log in to the VPC console and choose **Subnets** from the navigation pane.

   b. Search for the name of the subnet where the node is located in the search box and click the subnet name.

   c. Confirm and modify the DNS server address. You are advised to add the default private DNS.

.. |image1| image:: /_static/images/en-us_image_0000002516198045.png
