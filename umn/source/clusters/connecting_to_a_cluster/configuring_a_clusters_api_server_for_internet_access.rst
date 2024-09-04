:original_name: cce_10_0864.html

.. _cce_10_0864:

Configuring a Cluster's API Server for Internet Access
======================================================

You can bind an EIP to an API server of a Kubernetes cluster so that the API server can access the Internet.

Procedure
---------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. On the **Overview** page, locate the **Connection Info** area, and click **Bind** next to **EIP**.
#. Select an existing EIP. If no EIP is available, click **Create EIP** to go to the EIP console and assign one.

   .. note::

      -  Binding an EIP to an API server for Internet access can pose a risk to the cluster's security. To mitigate this risk, configure Advanced Anti-DDoS or API server access policies (:ref:`Configuring Access Policies for an API Server <cce_10_0864__section1953155515411>`) for the bound EIP.
      -  Binding an EIP to an API server will cause the API server to restart briefly and update the kubeconfig certificate. Do not make any changes to the cluster during this period.

#. Click **OK**.

.. _cce_10_0864__section1953155515411:

Configuring Access Policies for an API Server
---------------------------------------------

To ensure the security of a cluster's API server, it is important to modify the security group rules for the master nodes. This is because the EIP, which is exposed to the Internet, is at risk of being attacked.

#. Log in to the CCE console and click the cluster name to access the cluster console. On the **Overview** page, copy the cluster ID in the **Basic Info** area.
#. Log in to the VPC console. In the navigation pane, choose **Access Control** > **Security Groups**.
#. Select **Description** as the filter criterion and paste the cluster ID to search for the target security groups.
#. Locate the row that contains the security group (starting with *{CCE cluster name}*\ **-cce-control**) of the master node and click **Manage Rules** in the **Operation** column.
#. On the page displayed, locate the row that contains port 5443 and click **Modify** in the **Operation** column to modify its inbound rules.
#. Change the source IP address that can be accessed as required. For example, if the IP address used by the client to access the API Server is **100.*.*.\***, you can add an inbound rule for port 5443 and set the source IP address to **100.*.*.\***.

   .. note::

      In addition to the client IP address, the port must allow traffic from the CIDR blocks of the VPC, container, and the control plane of the hosted service mesh to ensure that the API Server can be accessed from within the cluster.

#. Click **Confirm**.
