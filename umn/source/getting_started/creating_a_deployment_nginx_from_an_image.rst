:original_name: cce_qs_0003.html

.. _cce_qs_0003:

Creating a Deployment (Nginx) from an Image
===========================================

You can use images to quickly create a single-pod workload that can be accessed from public networks. This section describes how to use CCE to quickly deploy an Nginx application and manage its life cycle.

Prerequisites
-------------

-  The Nginx image has been pushed to SWR.
-  You have created a CCE cluster that contains a node with 4 vCPUs and 8 GB memory. The node has an elastic IP address (EIP).

-  A cluster is a logical group of cloud servers that run workloads. Each cloud server is a node in the cluster.
-  For details on how to create a cluster, see :ref:`Creating a Kubernetes Cluster <cce_qs_0008>`.

Nginx Overview
--------------

Nginx is a lightweight web server. On CCE, you can quickly set up a Nginx web server.

This section uses the Nginx application as an example to describe how to create a workload. The creation takes about 5 minutes.

After Nginx is created successfully, you can access the Nginx web page.


.. figure:: /_static/images/en-us_image_0000001550558281.png
   :alt: **Figure 1** Nginx web page

   **Figure 1** Nginx web page

Procedure
---------

The following is the procedure for creating a containerized workload from a container image.

#. Log in to the CCE console.

#. Choose the target cluster.

#. In the navigation pane, choose **Workloads**. Then, click **Create Workload**.

#. Configure the following parameters and retain the default value for other parameters:

   **Basic Info**

   -  **Workload Type**: Select **Deployment**.
   -  **Workload Name**: Set it to **nginx**.
   -  **Namespace**: Select **default**.
   -  **Pods**: Set the quantity of pods to **1**.

   **Container Settings**

   Enter **nginx:latest** in the **Image Name** text box.

   **Service Settings**

   Click the plus sign (+) to create a Service for accessing the workload from an external network. In this example, create a LoadBalancer Service. Set the following parameters:

   -  **Service Name**: name of the Service exposed to external networks. In this example, the Service name is **nginx**.
   -  **Service Type**: Select **LoadBalancer**.
   -  **Service Affinity**: Retain the default value.
   -  **Load Balancer**: If a load balancer is available, select an existing load balancer. If not, choose **Auto create** to create one on the ELB console.
   -  **Port**:

      -  **Protocol**: Select **TCP**.
      -  **Service Port**: Set this parameter to **8080**, which is mapped to the container port.
      -  **Container Port**: port on which the application listens. For containers created using the Nginx image, set this parameter to **80**. For other applications, set this parameter to the port of the application.

#. Click **Create Workload**.

   Wait until the workload is created.

   The created Deployment will be displayed on the **Deployments** page.

Accessing Nginx
---------------

#. Obtain the external access address of Nginx.

   Click the Nginx workload to enter its details page. On the **Access Mode** tab page, you can view the IP address of Nginx. The public IP address is the external access address.

#. Enter the **external access address** in the address box of a browser. The following shows the welcome page if you successfully access the workload.


   .. figure:: /_static/images/en-us_image_0000001550558289.png
      :alt: **Figure 2** Accessing Nginx

      **Figure 2** Accessing Nginx
