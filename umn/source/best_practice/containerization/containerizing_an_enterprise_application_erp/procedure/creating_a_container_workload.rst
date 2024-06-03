:original_name: cce_bestpractice_0010.html

.. _cce_bestpractice_0010:

Creating a Container Workload
=============================

This section describes how to deploy a workload on CCE. When using CCE for the first time, create an initial cluster and add a node into the cluster.

.. note::

   Containerized workloads are deployed in a similar way. The difference lies in:

   -  Whether environment variables need to be set.
   -  Whether cloud storage is used.

Required Cloud Services
-----------------------

-  Cloud Container Engine (CCE): a highly reliable and high-performance service that allows enterprises to manage containerized applications. With support for Kubernetes-native applications and tools, CCE makes it simple to set up an environment for running containers in the cloud.
-  Elastic Cloud Server (ECS): a scalable and on-demand cloud server. It helps you to efficiently set up reliable, secure, and flexible application environments, ensuring stable service running and improving O&M efficiency.
-  Virtual Private Cloud (VPC): an isolated and private virtual network environment that users apply for in the cloud. You can configure the IP address ranges, subnets, and security groups, as well as assign elastic IP addresses and allocate bandwidth in a VPC.

Basic Concepts
--------------

-  A cluster is a collection of computing resources, including a group of node resources. A container runs on a node. Before creating a containerized application, you must have an available cluster.
-  A node is a virtual or physical machine that provides computing resources. You must have sufficient node resources to ensure successful operations such as creating applications.
-  A workload indicates a group of container pods running on CCE. CCE supports third-party application hosting and provides the full lifecycle (from deployment to O&M) management for applications. This section describes how to use a container image to create a workload.

Procedure
---------

#. .. _cce_bestpractice_0010__li1025612329217:

   Prepare the environment as described in :ref:`Table 1 <cce_bestpractice_0010__table1399518309317>`.

   .. _cce_bestpractice_0010__table1399518309317:

   .. table:: **Table 1** Preparing the environment

      +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | No.                   | Category              | Procedure                                                                                                                                                                                      |
      +=======================+=======================+================================================================================================================================================================================================+
      | 1                     | Creating a VPC        | Create a VPC before you create a cluster. A VPC provides an isolated, configurable, and manageable virtual network environment for CCE clusters.                                               |
      |                       |                       |                                                                                                                                                                                                |
      |                       |                       | If you have a VPC already, skip to the next task.                                                                                                                                              |
      |                       |                       |                                                                                                                                                                                                |
      |                       |                       | a. Log in to the management console.                                                                                                                                                           |
      |                       |                       | b. In the service list, choose **Networking** > **Virtual Private Cloud**.                                                                                                                     |
      |                       |                       | c. On the **Dashboard** page, click **Create VPC**.                                                                                                                                            |
      |                       |                       | d. Follow the instructions to create a VPC. Retain default settings for parameters unless otherwise specified.                                                                                 |
      +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | 2                     | Creating a key pair   | Create a key pair before you create a containerized application. Key pairs are used for identity authentication during remote login to a node. If you have a key pair already, skip this task. |
      |                       |                       |                                                                                                                                                                                                |
      |                       |                       | a. Log in to the management console.                                                                                                                                                           |
      |                       |                       |                                                                                                                                                                                                |
      |                       |                       | b. In the service list, choose **Data Encryption Workshop** under **Security & Compliance**.                                                                                                   |
      |                       |                       |                                                                                                                                                                                                |
      |                       |                       | c. In the navigation pane, choose **Key Pair Service**. On the **Private Key Pairs** tab, click **Create Key Pair**.                                                                           |
      |                       |                       |                                                                                                                                                                                                |
      |                       |                       | d. Enter a key pair name, select **I agree to have the private key managed on the cloud** and **I have read and agree to the Key Pair Service Disclaimer**, and click **OK**.                  |
      |                       |                       |                                                                                                                                                                                                |
      |                       |                       | e. In the dialog box displayed, click **OK**.                                                                                                                                                  |
      |                       |                       |                                                                                                                                                                                                |
      |                       |                       |    View and save the key pair. For security purposes, a key pair can be downloaded only once. Keep it secure to ensure successful login.                                                       |
      +-----------------------+-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Create a cluster and a node.

   a. Log in to the CCE console. On the **Clusters** page, click **Create** **Cluster** and select the type for the cluster to be created.

      Configure cluster parameters and select the VPC created in :ref:`1 <cce_bestpractice_0010__li1025612329217>`.

   b. Create a node and select the key pair created in :ref:`1 <cce_bestpractice_0010__li1025612329217>` as the login option.

#. Deploy a workload on CCE.

   a. Log in to the CCE console and click the name of the cluster to access the cluster console. In the navigation pane, choose **Workloads** and click **Create Workload**.

   b. Configure the following parameters, and retain the default settings for other parameters:

      -  **Workload Name**: Set it to **apptest**.
      -  **Pods**: Set it to **1**.

   c. In the **Container Settings** area, select the image uploaded in :ref:`Building and Uploading an Image <cce_bestpractice_0009>`.

   d. In the **Container Settings** area, choose **Environment Variables** and add environment variables for interconnecting with the MySQL database. The environment variables are set in the :ref:`startup script <cce_bestpractice_0007__li12314175319811>`.

      .. note::

         In this example, interconnection with the MySQL database is implemented through configuring the environment variables. Determine whether to use environment variables based on your service requirements.

      .. table:: **Table 2** Configuring environment variables

         ============== ===========================================
         Variable Name  Variable Value/Variable Reference
         ============== ===========================================
         MYSQL_DB       Database name.
         MYSQL_URL      IP address and port number of the database.
         MYSQL_USER     Database username.
         MYSQL_PASSWORD Database user password.
         ============== ===========================================

   e. In the **Container Settings** area, choose **Data Storage** and configure cloud storage for persistent data storage.

      .. note::

         In this example, the MongoDB database is used and persistent data storage is also needed, so you need to configure cloud storage. Determine whether to use cloud storage based on your service requirements.

      The mounted path must be the same as the MongoDB storage path in the Docker startup script. For details, see the :ref:`startup script <cce_bestpractice_0007__li12314175319811>`. In this example, the path is **/usr/local/mongodb/data**.

   f. In the **Service Settings** area, click |image1| to add a service, configure workload access parameters, and click **OK**.

      .. note::

         In this example, the application will be accessible from public networks by using an elastic IP address.

      -  **Service Name**: name of the application that can be accessed externally. In this example, this parameter is set to **apptest**.
      -  **Service Type**: In this example, select **NodePort**.
      -  **Service Affinity**

         -  **Cluster-level**: The IP addresses and access ports of all nodes in a cluster can be used to access the workload associated with the Service. Service access will cause performance loss due to route redirection, and the source IP address of the client cannot be obtained.
         -  **Node-level**: Only the IP address and access port of the node where the workload is located can be used to access the workload associated with the Service. Service access will not cause performance loss due to route redirection, and the source IP address of the client can be obtained.

      -  **Port**

         -  **Protocol**: Set it to **TCP**.
         -  **Service Port**: port for accessing the Service.
         -  **Container Port**: port that the application will listen on the container. In this example, this parameter is set to **8080**.
         -  **Node Port**: Set it to **Auto**. The system automatically opens a real port on all nodes in the current cluster and then maps the port number to the container port.

   g. Click **Create Workload**.

      After the workload is created, you can view the running workload in the workload list.

Verifying a Workload
--------------------

After a workload is created, you can access the workload to check whether the deployment is successful.

In the preceding configuration, the NodePort mode is selected to access the workload by using **IP address:Port number**. If the access is successful, the workload is successfully deployed.

You can obtain the access mode from the **Access Mode** tab on the workload details page.

.. |image1| image:: /_static/images/en-us_image_0000001897904609.png
