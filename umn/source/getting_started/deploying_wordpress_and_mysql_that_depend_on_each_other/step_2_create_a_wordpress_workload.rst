:original_name: cce_qs_0005.html

.. _cce_qs_0005:

Step 2: Create a WordPress Workload
===================================

WordPress was originally a blog platform based on PHP and MySQL. It is gradually evolved into a content management system. You can set up your own blog website on any server that supports PHP and MySQL. Thousands of plug-ins and countless theme templates are available for WordPress and easy to install.

This section describes how to create a public WordPress website from images.

Prerequisites
-------------

-  The WordPress and MySQL images have been pushed to SWR.
-  You have created a CCE cluster that contains a node with 4 vCPUs and 8 GB memory. For details on how to create a cluster, see :ref:`Creating a Kubernetes Cluster <cce_qs_0008>`.
-  The MySQL database has been created by following the instructions in :ref:`Step 1: Create a MySQL Workload <cce_qs_0004>`. In this example, WordPress data is stored in the MySQL database.

Creating a WordPress Blog Website
---------------------------------

#. Log in to the CCE console.

#. Choose the target cluster.

#. In the navigation pane, choose **Workloads**. Then, click **Create Workload**.

#. Set workload parameters.

   **Basic Info**

   -  **Workload Type**: Select **Deployment**.
   -  **Workload Name**: Set it to **wordpress**.
   -  **Namespace**: Select **default**.
   -  **Pods**: Set this parameter to **2** in this example.

   **Container Settings**

   Enter **wordpress:php7.3** in the **Image Name** text box.

   Add the following environment variables:

   (These variables let WordPress know the information about the MySQL database.)

   -  **WORDPRESS_DB_HOST**: address for accessing the database, which can be found in the Service (on the **Services** tab page) of the MySQL workload. You can use the internal domain name **mysql.default.svc.cluster.local:3306** to access the database, or use only **mysql:3306** omitting **.default.svc.cluster.local**.
   -  **WORDPRESS_DB_USER**: username for accessing the database. The value must be the same as that of **MYSQL_USER** in :ref:`Step 1: Create a MySQL Workload <cce_qs_0004>`, which is used to connect to MySQL.
   -  **WORDPRESS_DB_PASSWORD**: password for accessing the database. The value must be the same as that of **MYSQL_PASSWORD** in :ref:`Step 1: Create a MySQL Workload <cce_qs_0004>`.
   -  **WORDPRESS_DB_NAME**: name of the database to be accessed. The value must be the same as that of **MYSQL_DATABASE** in :ref:`Step 1: Create a MySQL Workload <cce_qs_0004>`.

   **Service Settings**

   Click the plus sign (+) to create a Service for accessing the workload from an external network. In this example, create a LoadBalancer Service. Set the following parameters:

   -  **Service Name**: name of the Service exposed to external networks. In this example, the Service name is **wordpress**.
   -  **Service Type**: Select **LoadBalancer**.
   -  **Service Affinity**: Retain the default value.
   -  **Load Balancer**: If a load balancer is available, select an existing load balancer. If not, choose **Auto create** to create one on the ELB console.
   -  **Port**:

      -  **Protocol**: Select **TCP**.
      -  **Service Port**: Set this parameter to **80**, which is mapped to the container port.
      -  **Container Port**: port on which the application listens. For containers created using the wordpress image, set this parameter to **80**. For other applications, set this parameter to the port of the application.

#. Click **Create Workload**.

   Wait until the workload is created.

   The created Deployment will be displayed on the **Deployments** page.

Accessing WordPress
-------------------

#. Obtain the external access address of WordPress.

   Click the wordpress workload to enter its details page. On the **Access Mode** tab page, you can view the IP address of WordPress. The load balancer IP address is the external access address.

#. Enter the external access address in the address box of a browser to connect to the workload.

   The following figure shows the accessed WordPress page.


   .. figure:: /_static/images/en-us_image_0000001550838141.png
      :alt: **Figure 1** WordPress workload

      **Figure 1** WordPress workload


   .. figure:: /_static/images/en-us_image_0000001550678265.png
      :alt: **Figure 2** WordPress

      **Figure 2** WordPress

Deleting Resources
------------------

Until now, you have completed all the Getting Started walkthroughs and have understood how to use CCE. Fees are incurred while nodes are running. If the clusters used in the Getting Started walkthroughs are no longer in use, perform the following steps to delete them. If you will continue the CCE walkthroughs, retain the clusters.

#. Log in to the CCE console.
#. In the navigation pane on the left, choose **Clusters**.
#. Click |image1| next to a cluster and delete the cluster as prompted.

.. |image1| image:: /_static/images/en-us_image_0000001499758244.png
