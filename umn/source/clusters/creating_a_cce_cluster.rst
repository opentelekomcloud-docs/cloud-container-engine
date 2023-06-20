:original_name: cce_10_0028.html

.. _cce_10_0028:

Creating a CCE Cluster
======================

On the CCE console, you can easily create Kubernetes clusters. Kubernetes can manage container clusters at scale. A cluster manages a group of node resources.

In CCE, you can create a CCE cluster to manage VMs. By using high-performance network models, hybrid clusters provide a multi-scenario, secure, and stable runtime environment for containers.

Notes and Constraints
---------------------

-  During the node creation, software packages are downloaded from OBS using the domain name. You need to use a private DNS server to resolve the OBS domain name, and configure the DNS server address of the subnet where the node resides with a private DNS server address. When you create a subnet, the private DNS server is used by default. If you change the subnet DNS, ensure that the DNS server in use can resolve the OBS domain name.
-  You can create a maximum of 50 clusters in a single region.
-  After a cluster is created, the following items cannot be changed:

   -  Cluster type
   -  Number of master nodes in the cluster
   -  AZ of a master node
   -  Network configuration of the cluster, such as the VPC, subnet, container CIDR block, Service CIDR block, and kube-proxy (forwarding) settings
   -  Network model. For example, change **Tunnel network** to **VPC network**.

Procedure
---------

#. Log in to the CCE console. Choose **Clusters**. On the displayed page, click **Create** next to **CCE cluster**.

#. Set cluster parameters.

   **Basic Settings**

   -  **Cluster Name**

   -  **Cluster Version**: Select the Kubernetes version used by the cluster.

   -  **Cluster Scale**: maximum number of nodes that can be managed by the cluster.

   -  **HA**: distribution mode of master nodes. By default, master nodes are randomly distributed in different AZs to improve DR capabilities.

      You can also expand advanced settings and customize the master node distribution mode. The following two modes are supported:

      -  **Random**: Master nodes are created in different AZs for DR.
      -  **Custom**: You can determine the location of each master node.

         -  **Host**: Master nodes are created on different hosts in the same AZ.
         -  **Custom**: You can determine the location of each master node.

   **Network Settings**

   The cluster network settings cover nodes, containers, and Services. For details about the cluster networking and container network models, see :ref:`Overview <cce_10_0010>`.

   -  **Network Model**: CCE clusters support **VPC network** and **tunnel network** models. For details, see :ref:`VPC Network <cce_10_0283>` and :ref:`Container Tunnel Network <cce_10_0282>`.
   -  **VPC**: Select the VPC to which the cluster belongs. If no VPC is available, click **Create VPC** to create one. The VPC cannot be changed after creation.
   -  **Master Node Subnet**: Select the subnet where the master node is deployed. If no subnet is available, click **Create Subnet** to create one. The subnet cannot be changed after creation.
   -  **Container CIDR Block**: Set the CIDR block used by containers.
   -  **Service CIDR Block**: CIDR block for Services used by containers in the same cluster to access each other. The value determines the maximum number of Services you can create. The value cannot be changed after creation.

   **Advanced Settings**

   -  **Request Forwarding**: The IPVS and iptables modes are supported. For details, see :ref:`Comparing iptables and IPVS <cce_10_0349>`.
   -  **CPU Manager**: For details, see :ref:`Binding CPU Cores <cce_10_0351>`.
   -  **Certificate Authentication**:

      -  **Default**: The X509-based authentication mode is enabled by default. X509 is a commonly used certificate format.

      -  **Custom:** The cluster can identify users based on the header in the request body for authentication.

         You need to upload your **CA root certificate**, **client certificate**, and **private key** of the client certificate.

         .. caution::

            -  Upload a file **smaller than 1 MB**. The CA certificate and client certificate can be in **.crt** or **.cer** format. The private key of the client certificate can only be uploaded **unencrypted**.
            -  The validity period of the client certificate must be longer than five years.
            -  The uploaded CA certificate is used for both the authentication proxy and the kube-apiserver aggregation layer configuration. **If the certificate is invalid, the cluster cannot be created**.
            -  Starting from v1.25, Kubernetes no longer supports certificate authentication generated using the SHA1WithRSA or ECDSAWithSHA1 algorithm. You are advised to use the SHA256 algorithm.

   -  **Description**: The value can contain a maximum of 200 English characters.

#. Click **Next: Add-on Configuration**.

   **Domain Name Resolution**: Uses the :ref:`coredns <cce_10_0129>` add-on, installed by default, to resolve domain names and connect to the cloud DNS server.

   **Container Storage**: Uses the :ref:`everest <cce_10_0066>` add-on, installed by default, to provide container storage based on CSI and connect to cloud storage services such as EVS.

   **Service logs**

   -  Using ICAgent:

      A log collector provided by Application Operations Management (AOM), reporting logs to AOM and Log Tank Service (LTS) according to the log collection rules you configured.

      You can collect stdout logs as required.

   **Overload Control**: If overload control is enabled, concurrent requests are dynamically controlled based on the resource pressure of master nodes to keep them and the cluster available.

#. After setting the parameters, click **Next: Confirm**. After confirming that the cluster configuration information is correct, select **I have read and understand the preceding instructions** and click **Submit**.

   It takes about 6 to 10 minutes to create a cluster. You can click **Back to Cluster List** to perform other operations on the cluster or click **Go to Cluster Events** to view the cluster details.

Related Operations
------------------

-  After creating a cluster, you can use the Kubernetes command line (CLI) tool kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.
-  Add nodes to the cluster. For details, see :ref:`Creating a Node <cce_10_0363>`.
