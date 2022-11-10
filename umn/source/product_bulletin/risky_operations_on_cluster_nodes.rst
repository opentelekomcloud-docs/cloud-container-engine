:original_name: cce_bulletin_0054.html

.. _cce_bulletin_0054:

Risky Operations on Cluster Nodes
=================================

Precautions for Using a Cluster
-------------------------------

-  When performing operations such as creating, deleting, and scaling clusters, do not change user permission in the Identity and Access Management (IAM) console. Otherwise, these operations may fail.
-  The containerized network canal of CCE nodes uses a CIDR block as the CIDR block of the container network. This CIDR block can be configured during cluster creation and defaults to 172.16.0.0/16. The Docker service creates a docker0 bridge by default. The default docker0 address is 172.17.0.1. When creating a cluster, ensure that the CIDR block of the VPC in the cluster is different from the CIDR blocks of the container network docker0 bridge. If VPC peering connections are used, also ensure that the CIDR block of the peer VPC is different from the CIDR blocks of the container network docker0 bridge.
-  For a cluster of Kubernetes v1.15, the DNS server of nodes in the cluster uses the DNS address in the VPC subnet, not the CoreDNS address of Kubernetes. Ensure that the DNS address in the subnet exists and is configurable.
-  For a cluster of Kubernetes v1.17, a single-plane network is used for nodes. When a multi-plane network is used, if you bind a NIC to an ECS, you need to configure the NIC information on the node and restart the NIC.
-  Do not modify the security groups, Elastic Volume Service (EVS) disks, and other resources created by CCE. Otherwise, clusters may not function properly. The resources created by CCE are labeled **cce**, for example, **cce-evs-jwh9pcl7-***\***.
-  When adding a node, ensure that the DNS server in the subnet can resolve the domain name of the corresponding service. Otherwise, the node cannot be installed properly.

Precautions for Using a Node
----------------------------

Some of the resources on the node need to run some necessary Kubernetes system components and resources to make the node as part of your cluster. Therefore, the total number of node resources and the number of assignable node resources in Kubernetes are different. The larger the node specifications, the more the containers deployed on the node. Therefore, Kubernetes needs to reserve more resources.

To ensure node stability, CCE cluster nodes reserve some resources for Kubernetes components (such as kubelet, kube-proxy, and docker) based on node specifications.

.. note::

   You are advised not to install software or modify the operating system (OS) configuration on a cluster node. This may cause exceptions on Kubernetes components installed on the node, and make the node unavailable.

Risky Operations on Nodes
-------------------------

After logging in to a node created by CCE, do not perform the following operations. Otherwise, the node will become unready.

.. table:: **Table 1** Operations that will cause nodes to become unready

   +----+--------------------------------------------------------------------------+-------------------------------------------------------------------------------+------------------------------------------------------------------------------------------+
   | SN | Risky Operation                                                          | Impact                                                                        | Solution                                                                                 |
   +====+==========================================================================+===============================================================================+==========================================================================================+
   | 1  | Reinstall the operating system using the original image or another image | The node will become unready.                                                 | Delete the node and buy a new node.                                                      |
   +----+--------------------------------------------------------------------------+-------------------------------------------------------------------------------+------------------------------------------------------------------------------------------+
   | 2  | Modify OS configuration                                                  | The node will become unready.                                                 | Restore the original configuration or buy the node again.                                |
   +----+--------------------------------------------------------------------------+-------------------------------------------------------------------------------+------------------------------------------------------------------------------------------+
   | 3  | Delete the **opt** directory, **/var/paas** directory, or a data disk    | The node will become unready.                                                 | Delete the node and buy a new node.                                                      |
   +----+--------------------------------------------------------------------------+-------------------------------------------------------------------------------+------------------------------------------------------------------------------------------+
   | 4  | Format and partition a node disk                                         | The node will become unready.                                                 | Delete the node and buy a new node.                                                      |
   +----+--------------------------------------------------------------------------+-------------------------------------------------------------------------------+------------------------------------------------------------------------------------------+
   | 5  | Modify a security group                                                  | The node will become unready or the cluster will exhibit unexpected behavior. | Correct the security group settings based on security group settings of normal clusters. |
   +----+--------------------------------------------------------------------------+-------------------------------------------------------------------------------+------------------------------------------------------------------------------------------+
