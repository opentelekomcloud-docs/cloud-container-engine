:original_name: cce_10_0680.html

.. _cce_10_0680:

Adding a Container CIDR Block for a Cluster That Uses a VPC Network
===================================================================

Scenario
--------

If the container CIDR block configured during CCE cluster creation cannot meet service expansion requirements, you can add container CIDR blocks for the cluster.

Notes and Constraints
---------------------

-  This function is only available for clusters v1.19.16-r0 or later that use VPC networks.

Adding a Container CIDR Block for a CCE Standard Cluster
--------------------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. On the **Overview** page, locate the **Networking Configuration** area and click **Add**.
#. Configure the container CIDR block to be added. You can add multiple container CIDR blocks at a time.

   .. note::

      New container CIDR blocks cannot conflict with the Service CIDR blocks, VPC CIDR block, and existing container CIDR blocks.

#. Click **OK**.

Precautions for Adding a Container CIDR Block
---------------------------------------------

After adding a container CIDR block, check the network connectivity. Ensure that intra-cluster communications are normal and verify the access to external services to avoid service interruptions.

-  **Bidirectional connectivity test of pods across CIDR blocks**: Test the connectivity between **pods on the same node** and between **pods on different nodes**.

   -  Create two pods on the same node: one named **pod-old** using an IP address from the old CIDR block and the other named **pod-new** using an IP address from the new CIDR block. Check whether they can ping each other and verify port connectivity between them.

      .. code-block::

         # Ping the IP address of pod-new from pod-old.
         kubectl exec -it pod-old -- ping <pod-new-ip>
         # Test the service port (for example, 8080).
         kubectl exec -it pod-old -- curl <pod-new-ip>:8080

   -  Select two different nodes and create pods using IP addresses from the old and new CIDR blocks respectively. Repeat the ping and port tests to ensure that the cross-node pod communications are normal.

-  **Firewall, security group, and network policy checks**: After a new CIDR block is added, the network policy, firewall, and security groups may deny traffic from the new CIDR block. As a result, the pods using IP addresses from the new CIDR block are denied.

   -  Firewall/Security group check: Check the security groups and network ACLs of the cluster to ensure that the inbound and outbound traffic of the new CIDR block (including the ports for communications between pods and the ports of the NodePort/ClusterIP Services) is allowed.

   -  Network policy check: Check whether the network rules contain the new CIDR block. The network policy check is required only for clusters with :ref:`DataPlane V2 <cce_10_0945>` enabled.

      .. code-block::

         kubectl get networkpolicy --all-namespaces
         # Query details about the policy.
         kubectl describe networkpolicy <policy-name> -n <namespace>

-  **Service discovery and Service connectivity check**: Check whether the pods using IP addresses from the new CIDR block can access the cluster's Services and whether external services can access the pods through the Services.

-  **Security group/Access control policy verification and updates of external cloud services**: By default, the security groups/whitelists of external cloud services allow only the traffic of the original container CIDR block. Requests sent by pods using IP addresses from the new container CIDR block are denied. This is easy to be ignored after a CIDR block is added.

   The external cloud services include but are not limited to the database service (such as RDS), cache/middleware (such as Kafka), OBS, API Gateway, and VPC Peering/Direct Connect.
