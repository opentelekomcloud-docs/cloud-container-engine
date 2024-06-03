:original_name: cce_bestpractice_0003.html

.. _cce_bestpractice_0003:

Containerizing an Entire Application
====================================

This tutorial describes how to containerize an ERP system by migrating it from a VM to CCE.

No recoding or re-architecting is required. You only need to pack the entire application into a container image and deploy the container image on CCE.

Introduction
------------

In this example, the **enterprise management application** is developed by enterprise A. This application is provided for third-party enterprises for use, and enterprise A is responsible for application maintenance.

When a third-party enterprise needs to use this application, a suit of **Tomcat application** and **MongoDB database** must be deployed for the third-party enterprise. The MySQL database, used to store data of third-party enterprises, is provided by enterprise A.

.. _cce_bestpractice_0003__fig78809934014:

.. figure:: /_static/images/en-us_image_0000001851585812.png
   :alt: **Figure 1** Application architecture

   **Figure 1** Application architecture

As shown in :ref:`Figure 1 <cce_bestpractice_0003__fig78809934014>`, the application is a standard Tomcat application, and its backend interconnects with MongoDB and MySQL databases. For this type of applications, there is no need to split the architecture. The entire application is built as an image, and the MongoDB database is deployed in the same image as the Tomcat application. In this way, the application can be deployed or upgraded through the image.

-  Interconnecting with the MongoDB database for storing user files.
-  Interconnecting with the MySQL database for storing third-party enterprise data. The MySQL database is an external cloud database.

Benefits
--------

In this example, the application was deployed on a VM. During application deployment and upgrade, a series of problems is encountered, but application containerization has solved these problems.

By using containers, you can easily pack application code, configurations, and dependencies and convert them into easy-to-use building blocks. This achieves the environmental consistency and version management, as well as improves the development and operation efficiency. Containers ensure quick, reliable, and consistent deployment of applications and prevent applications from being affected by deployment environment.

.. table:: **Table 1** Comparison between the two deployment modes

   +---------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Category                        | Before: Application Deployment on VM                                                                                                                                           | After: Application Deployment Using Containers                                                                                                                          |
   +=================================+================================================================================================================================================================================+=========================================================================================================================================================================+
   | Deployment                      | High deployment cost.                                                                                                                                                          | More than 50% cost reduced.                                                                                                                                             |
   |                                 |                                                                                                                                                                                |                                                                                                                                                                         |
   |                                 | A VM is required for deploying a system for a customer.                                                                                                                        | Container services achieve multi-tenant isolation, which allows you to deploy systems for different enterprises on the same VM.                                         |
   +---------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Upgrade                         | Low upgrade efficiency.                                                                                                                                                        | Per-second level upgrade.                                                                                                                                               |
   |                                 |                                                                                                                                                                                |                                                                                                                                                                         |
   |                                 | During version upgrades, log in to VMs one by one and manually configure the upgrades, which is inefficient and error-prone.                                                   | Version upgrades can be completed within seconds by replacing the image tag. In addition, CCE provides rolling updates, ensuring zero service downtime during upgrades. |
   +---------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Operation and maintenance (O&M) | High O&M cost.                                                                                                                                                                 | Automatic O&M                                                                                                                                                           |
   |                                 |                                                                                                                                                                                |                                                                                                                                                                         |
   |                                 | As the number of applications deployed for customer grows, the number of VMs that need to be maintained increases accordingly, which requires a large sum of maintenance cost. | Enterprises can focus on service development without paying attention to VM maintenance.                                                                                |
   +---------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
