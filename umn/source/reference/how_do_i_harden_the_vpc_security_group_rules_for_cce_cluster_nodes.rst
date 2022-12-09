:original_name: cce_faq_00265.html

.. _cce_faq_00265:

How Do I Harden the VPC Security Group Rules for CCE Cluster Nodes?
===================================================================

CCE is a universal container platform. Its default security group rules apply to common scenarios. Based on security requirements, you can harden the security group rules set for CCE clusters on the **Security Groups** page of **Network Console**.

.. note::

   To view security groups, log in to the CCE console, choose **Service List** > **Network** > **Virtual Private Cloud**, and choose **Access Control** > **Security Groups** in the navigation pane.

The security group name of a master node is {Cluster name}-cce-control-{Random ID}. The security group name of a worker node is {Cluster name}-cce-node-{Random ID}.

Enable the following ports in security groups:

**For {Cluster name}-cce-control-{Random ID}:**

-  The source IP addresses defined in the security group rules must be permitted.
-  **4789**: used for network access between containers.
-  **5443** and **5444**: ports to which kube-apiserver of the master node listens. The two ports must permit requests from VPC and container CIDR blocks and control plane CIDR blocks of the hosting mesh.
-  **9443**: used by canal of the node to listen to canal-api of the master node.
-  **8445**: used by storage_driver of the node to access csms-storagemgr of the master node.

**For {Cluster name}-cce-node-{Random ID}:**

-  The source IP addresses defined in the security group rules must be permitted.
-  **4789**: used for network access between containers.
-  **10250**: used by the master node to proactively access kubelet of the node (for example, by running **kubectl exec {pod}**).
-  **30000** to **32767** must permit requests from VPC, container, and ELB CIDR blocks.
