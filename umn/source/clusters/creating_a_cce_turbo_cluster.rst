:original_name: cce_10_0298.html

.. _cce_10_0298:

Creating a CCE Turbo Cluster
============================

CCE Turbo clusters run on a cloud native infrastructure that features software-hardware synergy to support passthrough networking, high security and reliability, and intelligent scheduling.

CCE Turbo clusters are paired with the Cloud Native Network 2.0 model for large-scale, high-performance container deployment. Containers are assigned IP addresses from the VPC CIDR block. Containers and nodes can belong to different subnets. Access requests from external networks in a VPC can be directly routed to container IP addresses, which greatly improves networking performance. **It is recommended** that you go through :ref:`Cloud Native Network 2.0 <cce_10_0284>` to understand the features and network planning of each CIDR block of Cloud Native Network 2.0.

Notes and Constraints
---------------------

-  During the node creation, software packages are downloaded from OBS using the domain name. You need to use a private DNS server to resolve the OBS domain name, and configure the subnet where the node resides with a private DNS server address. When you create a subnet, the private DNS server is used by default. If you change the subnet DNS, ensure that the DNS server in use can resolve the OBS domain name.
-  You can create a maximum of 50 clusters in a single region.
-  CCE Turbo clusters support only Cloud Native Network 2.0. For details about this network model, see :ref:`Cloud Native Network 2.0 <cce_10_0284>`.
-  After a cluster is created, the following items cannot be changed:

   -  Cluster type
   -  Number of master nodes in the cluster
   -  AZ of a master node
   -  Network configuration of the cluster, such as the VPC, subnet, container CIDR block, Service CIDR block, and kube-proxy (forwarding) settings.
   -  Network model. For example, change **Tunnel network** to **VPC network**.

Procedure
---------

#. Log in to the CCE console. Choose **Clusters**. On the displayed page, click **Create** next to **CCE Turbo cluster**.

#. Specify cluster parameters.

   **Basic Settings**

   -  **Cluster Name**

   -  **Cluster Version**: Select the Kubernetes version used by the cluster.

   -  **Cluster Scale**: Select the maximum number of nodes that can be managed by the cluster. After the creation is complete, only scale-out is supported, but not scale-in.

   -  **HA**: distribution mode of master nodes. By default, master nodes are randomly distributed in different AZs to improve DR capabilities.

      You can also expand advanced settings and customize the master node distribution mode. The following modes are supported:

      -  **Host**: Master nodes are created on different hosts in the same AZ.
      -  **Custom**: You can determine the location of each master node.

   **Network Settings**

   The cluster network settings cover nodes, containers, and Services. For details about the cluster networking and container network models, see :ref:`Overview <cce_10_0010>`.

   -  **Network Model**: CCE Turbo clusters support only **Cloud Native Network 2.0**. For details, see :ref:`Cloud Native Network 2.0 <cce_10_0284>`.
   -  **VPC**: Select the VPC to which the cluster belongs. If no VPC is available, click **Create VPC** to create one. The value cannot be changed after creation.
   -  **Master Node Subnet**: Select the subnet where the master node is deployed. If no subnet is available, click **Create Subnet** to create one. A master node requires at least four IP addresses, which cannot be changed after creation.
   -  **Pod Subnet**: Select the subnet where the container is located. If no subnet is available, click **Create Subnet** to create one. The pod subnet determines the maximum number of containers in the cluster. You can add pod subnets after creating the cluster.
   -  **Service CIDR Block**: CIDR block for :ref:`Services <cce_10_0249>` used by containers in the same cluster to access each other. The value determines the maximum number of Services you can create. The value cannot be changed after creation.

   **Advanced Settings**

   -  **Request Forwarding**: The IPVS and iptables modes are supported. For details, see :ref:`Comparing iptables and IPVS <cce_10_0349>`.

   -  **CPU Manager**: For details, see :ref:`Binding CPU Cores <cce_10_0351>`.

   -  **Resource Tag**:

      You can add resource tags to classify resources.

      You can create **predefined tags** in Tag Management Service (TMS). Predefined tags are visible to all service resources that support the tagging function. You can use predefined tags to improve tag creation and resource migration efficiency.

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

   By default, :ref:`cordens <cce_10_0129>` and :ref:`everest <cce_10_0066>` add-ons are installed.

   **Service log**

   -  **ICAgent**:

      A log collector provided by Application Operations Management (AOM), reporting logs to AOM and Log Tank Service (LTS) according to the log collection rules you configured.

      You can collect stdout logs as required.

#. After configuring the parameters, click **Next: Confirm**.

   It takes about 6 to 10 minutes to create a cluster. You can click **Back to Cluster List** to perform other operations on the cluster or click **Go to Cluster Events** to view the cluster details.

Related Operations
------------------

-  Using kubectl to connect to the cluster: :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`
-  Add nodes to the cluster. For details, see :ref:`Creating a Node <cce_10_0363>`.
