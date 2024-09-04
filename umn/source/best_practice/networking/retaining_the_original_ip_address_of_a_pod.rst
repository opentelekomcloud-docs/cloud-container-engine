:original_name: cce_bestpractice_10041.html

.. _cce_bestpractice_10041:

Retaining the Original IP Address of a Pod
==========================================

SNAT Overview
-------------

In a CCE cluster that uses a VPC network, to enable pods to communicate with external systems, the system automatically translates the source IP addresses of the pods into the IP addresses of the nodes that are running them. This allows pods to communicate with external systems using the node IP addresses. This process is known as pod IP address masquerading or Source Network Address Translation (SNAT).

Prerequisites
-------------

You have a cluster that uses the VPC network and whose version is v1.23.14-r0, v1.25.9-r0, v1.27.6-r0, v1.28.4-r0, or later.

Default Non-Masqueraded CIDR Block Settings in a CCE Cluster
------------------------------------------------------------

By default, CCE uses the following well-known private CIDR blocks as non-masqueraded CIDR blocks in each cluster:

-  **10.0.0.0/8**
-  **172.16.0.0/12**
-  **192.168.0.0/16**

Additionally, in a CCE cluster that uses a secondary VPC CIDR block, adding or resetting a node will automatically include the secondary CIDR block in the non-masqueraded CIDR blocks.

This means that when a pod initiates communication from these CIDR blocks with external resources, its source IP address of the data packet remains unchanged, and it will not be replaced with the IP address of the node running the pod.

Scenarios Where the Default Non-Masqueraded CIDR Blocks Does Not Fit
--------------------------------------------------------------------

The default non-masqueraded CIDR block settings in CCE clusters apply to typical scenarios, but in certain specific scenarios, these default settings may not be sufficient to meet user requirements. The following shows typical examples:

-  Cross-node access to pods in a cluster

   When a node in a Kubernetes cluster needs to access a pod on another node, the response data packet sent from the pod is automatically subject to SNAT. This changes the source IP address from the pod IP address to the IP address of the node that runs the pod. However, this automatic IP address translation can sometimes lead to communication issues, making cross-node access impossible.

   To enable a node to access pods on other nodes, you can add the CIDR block of the subnet where the node is located to the **nonMasqueradeCIDRs** parameter. This will skip SNAT and allow the original IP addresses of pods on these nodes to be retained.

-  Access from other resources in the same VPC as a cluster to pods in the cluster

   In certain scenarios, it may be necessary to access the original IP addresses of pods on different nodes in a CCE cluster directly from other resources (such as ECSs) in the same VPC as the cluster. However, with SNAT enabled by default, the source IP addresses of the data packets are replaced with the IP addresses of the nodes that run these pods when the data packets pass through the nodes. This makes it difficult for these resources to access pods directly.

   To enable direct access from resources in the same VPC as the cluster to pods, you can add the CIDR blocks of the subnets where these resources are located to the **nonMasqueradeCIDRs** parameter. This will skip SNAT and ensure that the source IP addresses of the data packets remain the same as the original IP addresses of pods.

Precautions
-----------

If you have set up a security group or ACL for a cloud service that only allows access from IP addresses of nodes running pods, there is no need to perform pod IP address masquerading and configure the **nonMasqueradeCIDRs** parameter.

The default setting of pod IP address masquerading (SNAT) is usually sufficient. However, if you need to retain the original IP addresses of pods in specific scenarios, you can configure the **nonMasqueradeCIDRs** parameter.

Before doing so, make sure you have evaluated your application scenario and understood the potential risks of improper configuration, because it may block access within clusters. If you are unsure whether to configure this parameter, it is recommended that you keep the default settings and adjust the configuration later once the requirement is clarified.

Procedure
---------

To reserve the source IP address of a pod when the pod accesses a CIDR block, you can configure **nonMasqueradeCIDRs** to specify the CIDR block that does not need to be masqueraded.

#. Log in to the CCE console and click the name of the target cluster to access the cluster console.
#. In the navigation pane, choose **Settings** and click the **Network** tab.
#. Modify the parameters under **Retain the non-spoofed CIDR block of the original pod IP address**. Make sure the parameter configuration complies with the following rules:

   -  Each CIDR block must comply with the CIDR format and must be a valid IPv4 CIDR block.

      Example of a correct CIDR block: **192.168.1.0/24**

      Example of an incorrect CIDR block: **192.168.1.1/24** (incompliant with the CIDR format)

   -  The CIDR blocks you configured do not overlap with each other.

      Example of correct CIDR blocks: **192.168.1.0/24** and **192.168.2.0/24**

      Example of incorrect CIDR blocks: **192.168.1.0/24** and **192.168.1.128/25** (The two CIDR blocks overlap.)

   -  The **nonMasqueradeCIDRs** parameter must contain all destination CIDR blocks that you want them to use the original pod IP addresses for communications.

#. After the modification, click **Confirm configuration**. The setting takes effect within 1 minute.
