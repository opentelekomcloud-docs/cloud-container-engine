:original_name: cce_qs_0004.html

.. _cce_qs_0004:

Step 1: Create a MySQL Workload
===============================

WordPress must be used together with MySQL. WordPress runs the content management program while MySQL serves as a database to store data.

Prerequisites
-------------

-  The WordPress and MySQL images have been pushed to SWR.
-  You have created a CCE cluster that contains a node with 4 vCPUs and 8 GB memory. For details on how to create a cluster, see :ref:`Creating a Kubernetes Cluster <cce_qs_0008>`.

Creating a MySQL Workload
-------------------------

#. Log in to the CCE console.

#. Choose the target cluster.

#. In the navigation pane, choose **Workloads**. Then, click **Create Workload**.

#. Set workload parameters.

   **Basic Info**

   -  **Workload Type**: Select **Deployment**.
   -  **Workload Name**: Set it to **mysql**.
   -  **Namespace**: Select **default**.
   -  **Pods**: Change the value to **1** in this example.

   **Container Settings**

   Enter **mysql:5.7** in the **Image Name** text box.

   Add the following four environment variables (details available in `MySQL <https://github.com/docker-library/docs/tree/master/mysql>`__):

   -  **MYSQL_ROOT_PASSWORD**: password of the **root** user of MySQL.
   -  **MYSQL_DATABASE**: name of the database created during image startup.
   -  **MYSQL_USER**: name of the database user.
   -  **MYSQL_PASSWORD**: password of the database user.

   **Service Settings**

   Click the plus sign (+) to create a Service for accessing MySQL from WordPress.

   Select **ClusterIP** for **Service Type**, set **Service Name** to **mysql**, set both the **Container Port** and **Service Port** to **3306**, and click **OK**.

   The default access port in the MySQL image is 3306. In this example, both the container port and Service port are set to **3306** for convenience. The access port can be changed to another port.

   In this way, the MySQL workload can be accessed through **Service name:Access port** (**mysql:3306** in this example) from within the cluster.

#. Click **Create Workload**.

   Wait until the workload is created.

   The created Deployment will be displayed on the **Deployments** page.
