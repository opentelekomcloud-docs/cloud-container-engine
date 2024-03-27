:original_name: cce_10_0028.html

.. _cce_10_0028:

Creating a Cluster
==================

On the CCE console, you can easily create Kubernetes clusters. After a cluster is created, the master node is hosted by CCE. You only need to create worker nodes. In this way, you can implement cost-effective O&M and efficient service deployment.

Constraints
-----------

-  During the node creation, software packages are downloaded from OBS using the domain name. Use a private DNS server to resolve the OBS domain name, and configure the DNS server address of the subnet where the node resides with a private DNS server address. When you create a subnet, the private DNS server is used by default. If you change the subnet DNS, ensure that the DNS server in use can resolve the OBS domain name.
-  You can create a maximum of 50 clusters in a single region.
-  After a cluster is created, the following items cannot be changed:

   -  Cluster type
   -  Number of master nodes in the cluster
   -  AZ of a master node
   -  Network configuration of the cluster, such as the VPC, subnet, container CIDR block, Service CIDR block, and kube-proxy (:ref:`request forwarding <cce_10_0028__li1895772174715>`) settings.
   -  Network model. For example, change **Tunnel network** to **VPC network**.

Procedure
---------

#. Log in to the CCE console.

#. Choose **Clusters**. On the displayed page, select the type of the cluster to be created and click **Create**.

#. Specify cluster parameters.

   **Basic Settings**

   -  **Cluster Name**: indicates the name of the cluster to be created. The cluster name must be unique under the same account.

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

   -  **Network Model**: CCE clusters support **VPC network** and **Tunnel network**. CCE Turbo clusters support **Cloud Native Network 2.0.**. For details, see :ref:`Overview <cce_10_0281>`.
   -  **VPC**: Select the VPC to which the cluster belongs. If no VPC is available, click **Create VPC** to create one. The value cannot be changed after creation.
   -  **Master Node Subnet**: Select the subnet where the master node is deployed. If no subnet is available, click **Create Subnet** to create one. The subnet cannot be changed after creation.
   -  **Container CIDR Block** (CCE Cluster): Specify the CIDR block used by containers, which determines the maximum number of containers in the cluster.
   -  **Default Pod Subnet** (CCE Turbo Cluster): Select the subnet where the container is located. If no subnet is available, click **Create Subnet**. The pod subnet determines the maximum number of containers in the cluster. You can add pod subnets after creating the cluster.
   -  **IPv4 Service CIDR Block**: CIDR block for Services used by containers in the same cluster to access each other. The value determines the maximum number of Services you can create. The value cannot be changed after creation.

   **Advanced Settings**

   -  .. _cce_10_0028__li1895772174715:

      **Request Forwarding**: The IPVS and iptables modes are supported. For details, see :ref:`Comparing iptables and IPVS <cce_10_0349>`.

   -  **CPU Manager**: When enabled, CPU cores will be exclusively allocated to workload pods. For details, see :ref:`CPU Policy <cce_10_0351>`.

   -  **Resource Tag**:

      You can add resource tags to classify resources.

   -  **Certificate Authentication**:

      -  **Default**: The X509-based authentication mode is enabled by default. X509 is a commonly used certificate format.

      -  **Custom:** The cluster can identify users based on the header in the request body for authentication.

         Upload your **CA root certificate**, **client certificate**, and **private key** of the client certificate.

         .. caution::

            -  Upload a file **smaller than 1 MiB**. The CA certificate and client certificate can be in **.crt** or **.cer** format. The private key of the client certificate can only be uploaded **unencrypted**.
            -  The validity period of the client certificate must be longer than five years.
            -  The uploaded CA certificate is used for both the authentication proxy and the kube-apiserver aggregation layer configuration. **If the certificate is invalid, the cluster cannot be created**.
            -  Starting from v1.25, Kubernetes no longer supports certificate authentication generated using the SHA1WithRSA or ECDSAWithSHA1 algorithm. You are advised to use the SHA256 algorithm.

   -  **Description**: The description cannot exceed 200 characters.

#. Click **Next: Add-on Configuration**.

   **Domain Name Resolution**:

   -  **Domain Name Resolution**: The :ref:`coredns <cce_10_0129>` add-on is installed by default to resolve domain names and connect to the cloud DNS server.

   **Container Storage**: The :ref:`everest <cce_10_0066>` add-on is installed by default to provide container storage based on CSI and connect to cloud storage services such as EVS.

   **Fault Detection**: The :ref:`npd <cce_10_0132>` add-on is installed by default to provide node fault detection and isolation for the cluster, helping you identify node problems in a timely manner.

   **Data Plane Logs**

   -  Using ICAgent:

      A log collector provided by Application Operations Management (AOM), reporting logs to AOM and Log Tank Service (LTS) according to the log collection rules you configured.

      You can collect stdout logs as required.

   **Overload Control**: If enabled, concurrent requests are dynamically controlled based on the resource pressure of master nodes to keep them and the cluster available. For details, see :ref:`Cluster Overload Control <cce_10_0602>`.

#. After the parameters are specified, click **Next: Confirm**. The cluster resource list is displayed. Confirm the information and click **Submit**.

   It takes about 6 to 10 minutes to create a cluster. You can click **Back to Cluster List** to perform other operations on the cluster or click **Go to Cluster Events** to view the cluster details.

Related Operations
------------------

-  After creating a cluster, you can use the Kubernetes command line (CLI) tool kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.
-  Add nodes to the cluster. For details, see :ref:`Creating a Node <cce_10_0363>`.
