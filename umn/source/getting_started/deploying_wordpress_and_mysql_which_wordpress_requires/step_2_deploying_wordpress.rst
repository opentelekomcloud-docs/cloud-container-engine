:original_name: cce_qs_0005.html

.. _cce_qs_0005:

Step 2: Deploying WordPress
===========================

WordPress started as a blogging platform using PHP and MySQL, but it has evolved into a complete content management system. You can set up your own blog on any server that supports PHP and MySQL. WordPress offers thousands of plugins and an endless variety of theme templates, most of which can be installed with just a few clicks.

This section describes how to create a public WordPress website from images.

Prerequisites
-------------

-  You have created a CCE cluster that contains a node with 4 vCPUs and 8 GiB of memory. For details about how to create a cluster, see :ref:`Creating a Kubernetes Cluster <cce_qs_0008>`.
-  The MySQL database has been created by following the instructions in :ref:`Step 1: Deploying MySQL <cce_qs_0004>`. In this example, WordPress data is stored in the MySQL database.

Using the Console
-----------------

#. Log in to the CCE console.

#. Click the name of the cluster to access the cluster console.

#. In the navigation pane, choose **Workloads**. In the right pane, click **Create Workload** in the upper right corner.

#. Configure workload parameters.

   **Basic Info**

   -  **Workload Type**: Select **Deployment**.
   -  **Workload Name**: Enter **wordpress** in the text box.
   -  **Namespace**: Select **default**.
   -  **Pods**: Set this parameter to **2** in this example.

   **Container Settings**

   Enter **wordpress:php7.3** in the **Image Name** text box.

   Add environment variables.

   WordPress will get the information about the MySQL database with the following variables.

   -  **WORDPRESS_DB_HOST**: address for accessing the database, which can be found in the Service (on the **Services** tab) of the MySQL workload. You can use the internal domain name **mysql.default.svc.cluster.local:3306** to access the database, or use only **mysql:3306** omitting **.default.svc.cluster.local**.
   -  **WORDPRESS_DB_USER**: username for accessing the database. The value must be the same as that of **MYSQL_USER** in :ref:`Step 1: Deploying MySQL <cce_qs_0004>`, which is used to access MySQL.
   -  **WORDPRESS_DB_PASSWORD**: password for accessing the database. The value must be the same as that of **MYSQL_PASSWORD** in :ref:`Step 1: Deploying MySQL <cce_qs_0004>`.
   -  **WORDPRESS_DB_NAME**: name of the database to be accessed. The value must be the same as that of **MYSQL_DATABASE** in :ref:`Step 1: Deploying MySQL <cce_qs_0004>`.

   **Service Settings**

   In the **Service Settings** area, click the plus sign (+) and create a Service for external network access to the workload. This example shows how to create a LoadBalancer Service. Configure the below parameters in the sliding window on the right.

   -  **Service Name**: Enter **wordpress**. The name of the Service is exposed to external networks.
   -  **Service Type**: Select **LoadBalancer**.
   -  **Service Affinity**: Retain the default value.
   -  **Load Balancer**: If a load balancer is available, select an existing load balancer. If not, click **Create Load Balancer** to create one on the ELB console.
   -  **Ports**:

      -  **Protocol**: Select **TCP**.
      -  **Service Port**: Set this parameter to **80**, which is mapped to the container port.
      -  **Container Port**: port on which the application listens. For containers created using the wordpress image, set this parameter to **80**. For other applications, set this parameter to the port of the application.

#. Click **Create Workload**.

   Wait until the workload is created.

   The created workload in the **Running** state will be displayed on the **Deployments** tab.

Accessing WordPress
-------------------

#. Obtain the external access address of WordPress.

   Click the name of the **wordpress** workload to enter its details page. On the page displayed, click the **Access Mode** tab, view the WordPress IP address. The public IP address is the external access address.

#. In the address bar of a browser, enter *<external-access-address>*\ **:**\ *port* to access the application.

   The following figure shows the accessed WordPress page.


   .. figure:: /_static/images/en-us_image_0000002661852187.png
      :alt: **Figure 1** WordPress

      **Figure 1** WordPress


   .. figure:: /_static/images/en-us_image_0000002661852189.png
      :alt: **Figure 2** WordPress

      **Figure 2** WordPress

Deleting Resources
------------------

Until now, you have completed all the Getting Started walkthroughs and have understood how to use CCE. If you will continue the CCE walkthroughs, retain the clusters. If the clusters used in the walkthroughs are no longer in use, perform the following operations to delete them:

#. Log in to the CCE console.
#. In the navigation pane, choose **Clusters**.
#. Click |image1| next to the cluster to be deleted, select **Delete Cluster**, and confirm the information as prompted.

.. |image1| image:: /_static/images/en-us_image_0000002661852195.png
