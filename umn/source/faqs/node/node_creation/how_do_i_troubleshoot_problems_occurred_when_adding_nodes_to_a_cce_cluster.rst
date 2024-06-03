:original_name: cce_faq_00027.html

.. _cce_faq_00027:

How Do I Troubleshoot Problems Occurred When Adding Nodes to a CCE Cluster?
===========================================================================

Note
----

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

Check Item 1: Subnet Quota
--------------------------

**Symptom**

New nodes cannot be added to a CCE cluster, and a message is displayed indicating that the subnet quota is insufficient.

**Cause Analysis**

Example:

VPC CIDR block: 192.168.66.0/24

Subnet CIDR block: 192.168.66.0/24

In 192.168.66.0/24, all 251 private IP addresses have been used.

**Solution**

#. Expand the VPC.

   Log in to the console and choose **Virtual Private Cloud** from the service list. On the page displayed, locate the row containing the target VPC and click **Edit CIDR Block** in the **Operation** column.

#. Change the subnet mask to **16** and click **OK**.

#. Click the VPC name. On the **Summary** tab page, click the number next to **Subnets** on the right and click **Create Subnet** to create a subnet.

#. Return to the page for adding a node on the CCE console, and select the newly created subnet.

   .. note::

      a. Adding subnets to the VPC does not affect the use of the existing 192.168.66.0/24 CIDR block.

         You can select a new subnet when creating a CCE node. The new subnet has a maximum of 251 private IP addresses. If the number of private IP addresses cannot meet service requirements, you can add more subnets.

      b. Subnets in the same VPC can communicate with each other.

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
   #. On the page displayed, click the **EIPs** tab. In the EIP list, locate the row containing the target EIP, click **Unbind**, and click **Yes**.
   #. Return to the page for adding a node on the CCE console, select **Use existing** for **EIP**, and add the node again.

-  **Method 2:** Increase the EIP quota.

Check Item 3: Security Group
----------------------------

**Symptom**

A node cannot be added to a CCE cluster.

**Solution**

You can click the cluster name to view the cluster details. In the **Networking Configuration** area, click the icon next to **Default security group of the node** to check whether the default security group is deleted and whether the security group rules comply with :ref:`How Can I Configure a Security Group Rule in a Cluster? <cce_faq_00265>`.

If your account has multiple clusters and you need to manage network security policies of nodes in a unified manner, you can specify custom security groups.
