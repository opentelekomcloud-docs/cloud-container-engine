:original_name: cce_bestpractice_0002.html

.. _cce_bestpractice_0002:

Solution Overview
=================

This chapter provides CCE best practices to walk you through the application containerization.

What Is a Container?
--------------------

A container is a lightweight high-performance resource isolation mechanism implemented based on the Linux kernel. It is a built-in capability of the operating system (OS) kernel.

CCE is an enterprise-class container service based on open-source Kubernetes. It is a high-performance and high-reliability service through which enterprises can manage containerized applications. CCE supports native Kubernetes applications and tools, allowing you to easily set up a container runtime in the cloud.

Why Is a Container Preferred?
-----------------------------

-  More efficient use of system resources

   A container does not require extra costs such as fees for hardware virtualization and those for running a complete OS. Therefore, a container has higher resource usage. Compared with a VM with the same configurations, a container can run more applications.

-  Faster startup

   A container directly runs on the host kernel and does not need to start a complete OS. Therefore, a container can be started within seconds or even milliseconds, greatly saving the development, testing, and deployment time.

-  Consistent runtime environment

   A container image provides a complete runtime environment to ensure environment consistency. In this case, problems (for example, some code runs properly on machine A but fails to run on machine B) will not occur.

-  Easier application migration, maintenance, and scaling

   A consistent runtime environment makes application migration easier. In addition, the in-use storage and image technologies facilitate the reuse of repeated applications and simplifies the expansion of images based on base images.

Containerization Modes
----------------------

The following modes are available for containerizing applications:

-  Mode 1: Containerize a single application as a whole. Application code and architecture remain unchanged.
-  Mode 2: Separate the components that are frequently upgraded or have high requirements on auto scaling from an application, and then containerize these components.
-  Mode 3: Transform an application to microservices and then containerize the microservices one by one.

:ref:`Table 1 <cce_bestpractice_0002__table955211293219>` lists the advantages and disadvantages of the three modes.

.. _cce_bestpractice_0002__table955211293219:

.. table:: **Table 1** Containerization modes

   +----------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
   | Containerization Mode                                                                                                | Advantage                                                                                                                                                                                                                                                                                                                                                                          | Disadvantage                                                                                                                                    |
   +======================================================================================================================+====================================================================================================================================================================================================================================================================================================================================================================================+=================================================================================================================================================+
   | Method 1:                                                                                                            | -  Zero modification on services: The application architecture and code require no change.                                                                                                                                                                                                                                                                                         | -  Difficult to expand the entire architecture of an application. As the code size increases, code update and maintenance would be complicated. |
   |                                                                                                                      | -  The deployment and upgrade efficiency is improved. Applications can be packed as container images to ensure application environment consistency and improve deployment efficiency.                                                                                                                                                                                              | -  Difficult to launch new functions, languages, frameworks, and technologies.                                                                  |
   | Containerize a single application as a whole.                                                                        | -  Reduce resource costs: Containers use system resources more efficiently. Compared with a VM with the same configurations, a container can run more applications.                                                                                                                                                                                                                |                                                                                                                                                 |
   +----------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
   | Method 2:                                                                                                            | -  Progressive transformation: Reconstructing the entire architecture involves a heavy workload. This mode containerizes only a part of components, which is easy to accept for customers.                                                                                                                                                                                         | Need to decouple some services.                                                                                                                 |
   |                                                                                                                      | -  Flexible scaling: Application components that have high requirements on auto scaling are containerized. When the application needs to be scaled, you only need to scale the containers, which is flexible and reduces the required system resources.                                                                                                                            |                                                                                                                                                 |
   | Containerize first the application components that are frequently updated or have high requirements on auto scaling. | -  Faster rollout of new features: Application components that are frequently upgraded are containerized. In subsequent upgrades, only these containers need to be upgraded. This shortens the time to market (TTM) of new features.                                                                                                                                               |                                                                                                                                                 |
   +----------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+
   | Method 3:                                                                                                            | -  Independent scaling: After an application is split into microservices, you can independently increase or decrease the number of instances for each microservice.                                                                                                                                                                                                                | Need to transform the application to microservices, which involves a large number of changes.                                                   |
   |                                                                                                                      | -  Increased development speed: Microservices are decoupled from one another. Code development of a microservice does not affect other microservices.                                                                                                                                                                                                                              |                                                                                                                                                 |
   | Transform an application to microservices and then containerize the microservices one by one.                        | -  Security assurance through isolation: For an overall application, if a security vulnerability exists, attackers can use this vulnerability to obtain the permission to all functions of the application. However, in a microservice architecture, if a service is attacked, attackers can only obtain the access permission to this service, but cannot intrude other services. |                                                                                                                                                 |
   |                                                                                                                      | -  Breakdown isolation: If one microservice breaks down, other microservices can still run properly.                                                                                                                                                                                                                                                                               |                                                                                                                                                 |
   +----------------------------------------------------------------------------------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------+

**Mode 1** is used as an example in this tutorial to illustrate how to containerize an enterprise resource planning (ERP) system.
