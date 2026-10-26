:original_name: cce_bestpractice_0002.html

.. _cce_bestpractice_0002:

Solution Overview
=================

This section provides CCE best practices to walk you through the application containerization.

What Is a Container?
--------------------

A container is a lightweight high-performance resource isolation mechanism implemented based on the Linux kernel. It is a built-in capability of the operating system (OS) kernel.

CCE is an enterprise-class container service based on open-source Kubernetes. It is a high-performance and high-reliability service through which enterprises can manage containerized applications. CCE supports native Kubernetes applications and tools, allowing you to easily set up a container runtime in the cloud.

Why Is a Container Preferred?
-----------------------------

-  More efficient use of system resources

   A container does not need extra overheads such as hardware virtualization or running of a complete OS. This makes it more efficient in using system resources. Compared with a VM with the same configurations, a container can run more applications.

-  Faster startup

   A container directly runs on the host kernel and does not need to start a complete OS. Therefore, a container can be started within seconds or even milliseconds, greatly saving the development, testing, and deployment time.

-  Consistent runtime environment

   A container image provides a complete runtime environment to ensure environment consistency. In this case, problems (for example, some code runs properly on machine A but fails to run on machine B) will not occur.

-  Easier application migration, maintenance, and scaling

   A consistent runtime environment makes application migration easier. In addition, the in-use storage and image technologies facilitate the reuse of repeated applications and simplifies the expansion of images based on base images.

How to Containerize an Application
----------------------------------

To containerize an application, you can:

-  Containerize an entire monolithic application without changing the code and architecture.
-  Containerize first the application components that are frequently updated or have high auto scaling requirements.
-  Break the application down into microservices and then containerize them.

:ref:`Table 1 <cce_bestpractice_0002__table955211293219>` lists the advantages and disadvantages of the three modes.

.. _cce_bestpractice_0002__table955211293219:

.. table:: **Table 1** Containerization modes

   +-------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+
   | How to Containerize                                                                                               | Advantage                                                                                                                                                                                                                                                                                        | Disadvantage                                                                                                                              |
   +===================================================================================================================+==================================================================================================================================================================================================================================================================================================+===========================================================================================================================================+
   | Method 1:                                                                                                         | -  Zero modification: Application architectures and code require no changes.                                                                                                                                                                                                                     | -  Difficult to expand the entire architecture of an application. As code size increases, code update and maintenance become complicated. |
   |                                                                                                                   | -  Efficient deployment and upgrade: Applications can be packaged as container images to ensure consistent environment and improve deployment efficiency.                                                                                                                                        | -  Difficult to use new functions, languages, frameworks, and technologies.                                                               |
   | Containerize an entire monolithic application.                                                                    | -  Reduced costs: Containers use system resources more efficiently. Compared with VMs, a host with the same specifications can run more applications.                                                                                                                                            |                                                                                                                                           |
   +-------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+
   | Method 2:                                                                                                         | -  Progressive transformation: Reconstructing the entire architecture is labor-intensive. Progressive containerization is more feasible.                                                                                                                                                         | Services need to be partially decoupled.                                                                                                  |
   |                                                                                                                   | -  Flexible scaling: Containerizing components with high auto scaling requirements and just scaling these containers enhance flexibility and reduce the number of system resources needed.                                                                                                       |                                                                                                                                           |
   | Containerize first the application components that are frequently updated or have high auto scaling requirements. | -  Faster rollout: Containerizing the components that are updated frequently and only upgrading these containers enable faster rollout of new features.                                                                                                                                          |                                                                                                                                           |
   +-------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+
   | Method 3:                                                                                                         | -  Independent scaling: After an application is split into groups of microservices, you can adjust the number of pods for each microservice separately.                                                                                                                                          | Breaking down services into microservices requires numerous changes.                                                                      |
   |                                                                                                                   | -  Faster development: Microservices run independently. They do not impact each other during code development.                                                                                                                                                                                   |                                                                                                                                           |
   | Break an application down into microservices and then containerize them.                                          | -  Enhanced security through isolation: In a monolithic application, a security vulnerability can grant access to all functions. However, in microservices, if one service is attacked, only the permissions of that specific service are compromised, preventing intrusion into other services. |                                                                                                                                           |
   |                                                                                                                   | -  Stabler services: If one microservice breaks down, other microservices can still run properly.                                                                                                                                                                                                |                                                                                                                                           |
   +-------------------------------------------------------------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------+

**Mode 1** is used as an example in this tutorial to illustrate how to containerize an enterprise resource planning (ERP) system.
