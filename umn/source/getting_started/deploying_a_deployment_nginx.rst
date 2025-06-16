:original_name: cce_qs_0003.html

.. _cce_qs_0003:

Deploying a Deployment (Nginx)
==============================

You can use images to quickly create a single-pod workload that can be accessed from public networks. This section describes how to use CCE to quickly deploy an Nginx application and manage its lifecycle.

Prerequisites
-------------

You have created a CCE cluster that contains a node with 4 vCPUs and 8 GiB memory. The node is bound with an EIP.

A cluster is a logical group of cloud servers that run workloads. Each cloud server is a node in the cluster.

For details on how to create a cluster, see :ref:`Creating a Kubernetes Cluster <cce_qs_0008>`.

Nginx Overview
--------------

Nginx is a lightweight web server. On CCE, you can quickly set up a Nginx web server.

This section uses the Nginx application as an example to describe how to create a workload. The creation takes about 5 minutes.

After Nginx is created, you can access the Nginx web page.


.. figure:: /_static/images/en-us_image_0000001851742664.png
   :alt: **Figure 1** Nginx web page accessed

   **Figure 1** Nginx web page accessed

Creating Nginx on the CCE Console
---------------------------------

The following is the procedure for creating a containerized workload from a container image.

#. Log in to the CCE console.

#. Click the name of the target cluster to access the cluster console.

#. In the navigation pane, choose **Workloads**. In the right pane, click **Create Workload** in the upper right corner.

#. Configure the following parameters and retain the default value for other parameters:

   **Basic Info**

   -  **Workload Type**: Select **Deployment**.
   -  **Workload Name**: Set it to **nginx**.
   -  **Namespace**: Select **default**.
   -  **Pods**: Set the quantity of pods to **1**.

   **Container Settings**

   Enter **nginx:latest** in the **Image Name** text box.

   **Service Settings**

   Click the plus sign (+) to create a Service for accessing the workload from an external network. This example shows how to create a LoadBalancer. Configure the following parameters in the window that slides out from the right:

   -  **Service Name**: Enter **nginx**. The name of the Service is exposed to external networks.

   -  **Service Type**: Select **LoadBalancer**.

   -  **Service Affinity**: Retain the default value.

   -  **Load Balancer**: If a load balancer is available, select the existing load balancer. If not, select **Auto create** to create one.

   -  .. _cce_qs_0003__li1798975217321:

      **Ports**:

      -  **Protocol**: Select **TCP**.
      -  **Service Port**: In this example, set this parameter to **8080**. Load balancer will use this port to create a listener to provide an entry for external traffic.
      -  **Container Port**: listening port of the application. In this example, this parameter is set to **80**. If another application is used, the container port must be the same as the listening port provided by the application.

#. Click **Create Workload**.

   Wait until the workload is created.

   The created Deployment will be displayed on the **Deployments** tab.

#. Obtain the external access address of Nginx.

   Click the Nginx workload name to enter its details page. On the page displayed, click the **Access Mode** tab, view the IP address of Nginx. The public IP address is the external access address.

#. Enter *External access address*:*Service port* in the address box of the browser to access the application. The *Service port* is configured in :ref:`Ports <cce_qs_0003__li1798975217321>`.


   .. figure:: /_static/images/en-us_image_0000001898022793.png
      :alt: **Figure 2** Accessing Nginx

      **Figure 2** Accessing Nginx
