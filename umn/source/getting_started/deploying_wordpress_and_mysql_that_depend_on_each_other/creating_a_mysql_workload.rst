:original_name: cce_qs_0004.html

.. _cce_qs_0004:

Creating a MySQL Workload
=========================

WordPress must be used together with MySQL. WordPress runs the content management program while MySQL serves as a database to store data.

Prerequisites
-------------

You have created a CCE cluster that contains a node with 4 vCPUs and 8 GiB memory. For details on how to create a cluster, see :ref:`Creating a Kubernetes Cluster <cce_qs_0008>`.

Operations on the Console
-------------------------

#. Log in to the CCE console.

#. Click the name of the target cluster to access the cluster console.

#. In the navigation pane, choose **Workloads**. Then, click **Create Workload** in the upper right corner.

#. Configure the basic information about the workload.

   -  **Workload Type**: Select **StatefulSet**.
   -  **Workload Name**: Set it to **mysql**.
   -  **Namespace**: Select **default**.
   -  **Pods**: In this example, change the quantity to 1, which means, there is only one pod running in the **mysql** workload.

#. Configure the basic information about the container.

   Enter **mysql:5.7** in the **Image Name** text box.

#. Click **Environment Variables** and add four environment variables. You can check `MySQL <https://github.com/docker-library/docs/tree/master/mysql>`__ to view the environment variables that can be configured.

   -  **MYSQL_ROOT_PASSWORD**: password of the **root** user of the MySQL database, which can be customized.
   -  **MYSQL_DATABASE**: name of the database to be created when the image is started, which can be customized.
   -  **MYSQL_USER**: database user name, which can be customized.
   -  **MYSQL_PASSWORD**: database user password, which can be customized.

#. Click **Lifecycle** and configure **Startup Command**.

   -  Command:

      .. code-block::

         /bin/bash

   -  Running parameters:

      .. code-block::

         -c
         rm -rf /var/lib/mysql/lost+found;docker-entrypoint.sh mysqld;

#. Click **Data Storage**, click **Add Volume**, select **VolumeClaimTemplate (VTC)** from the drop-down list, and add an EVS disk for MySQL.

   Click **Create PVC** and configure the following parameters (Keep default for other parameters):

   -  **PVC Type**: Select **EVS**.
   -  **PVC Name**: Enter a name, for example, **mysql**.
   -  **Creation Mode**: Only **Dynamically provision** is supported.
   -  **Storage Classes**: The default value is **csi-disk**.
   -  **AZ**: Select an AZ. The EVS disk can only be attached to nodes in the same AZ. After an EVS disk is created, the AZ where the disk locates cannot be changed.
   -  **Disk Type**: Select a proper type as required.
   -  **Capacity (GiB)**: Enter the capacity as required. The default value is **10** GiB.

   Click **Create** and enter the path for mounting the storage volume to the container. The default path used by MySQL is **/var/lib/mysql**.

#. In the **Headless Service Parameters** area, configure a headless Service.

   A headless Service needs to be configured for the StatefulSet networking. The headless Service generates DNS name for each pod for accessing a specific StatefulSet pod. For a replicated MySQL database, the headless Service needs to be used to read and write the MySQL primary server, and copies existing data from other running replicas. In this example, there is only one pod running in the MySQL workload. Therefore, the headless Service is not used. In this case, enter **3306** for both the Service port and container port. For details about the replicated MySQL examples, see `Run a Replicated Stateful Application <https://kubernetes.io/docs/tasks/run-application/run-replicated-stateful-application/>`__.

#. In the **Service Settings** area, click the plus sign (+) and create a Service for accessing MySQL from WordPress.

   Select **ClusterIP** for **Service Type**, enter **mysql**\ in the **Service Name** text box, set both the **Container Port** and **Service Port** to **3306**, and click **OK**.

   The default access port in the MySQL image is 3306. In this example, both the container port and Service port are set to **3306** for convenience. The access port can be changed to another port.

   In this way, the MySQL workload can be accessed through *{Service name}*\ **:**\ *{Access port}* (for example, **mysql:3306**) from within the cluster.

#. Click **Create Workload**.

   Wait until the workload is created.

   The created Deployment will be displayed on the **StatefulSets** tab.
