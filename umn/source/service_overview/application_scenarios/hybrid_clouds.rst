:original_name: cce_productdesc_0018.html

.. _cce_productdesc_0018:

Hybrid Clouds
=============

Application Scenarios
---------------------

-  Multi-cloud deployment, DR, and backup

   To ensure high service availability, services need to be deployed on container services of multiple clouds. When a cloud is faulty, the unified traffic distribution mechanism automatically switches service traffic to other clouds.

-  Traffic distribution and auto scaling

   Large organizations often span cloud facilities in different regions. To optimize cost-efficiency, these facilities require auto scaling capabilities that respond dynamically to fluctuations in service demand.

-  Migration to the cloud and local data hosting

   In sectors like finance and healthcare, sensitive data is required to reside in local IDCs to comply with strict security and regulatory mandates. General-purpose services are deployed on the cloud to support high concurrency, fast responses, and centralized management.

-  Environment decoupling

   You may choose to deploy production environments on the cloud while keeping development environments in local IDCs to enhance IP address security.

Benefits
--------

Thanks to containers' environment-independent feature, CCE manages containers centrally, and these containers can access each other. You can seamlessly migrate your applications and data on and off the cloud. This meets complex service requirements for auto scaling, flexibility, security, and compliance. Additionally, resources in different cloud environments can be operated and maintained centrally, providing easier resource scheduling and DR.

Advantages
----------

-  On-cloud DR

   With CCE, service systems can be deployed on container services of multiple clouds for unified traffic distribution. If a cloud is faulty, service traffic can be automatically switched to others. Live network issues can be quickly and automatically resolved.

-  Unified architecture and auto scaling

   Unified architecture on and off the cloud can flexibly implement auto scaling, smooth migration, and resource expansion to handle traffic peaks.

-  Decoupling and sharing

   On CCE, data, environments, and compute are decoupled, including sensitive and general service data, development and production environments, as well as compute-intensive and general services. Auto scaling and unified cluster management enable resource and capability sharing both on and off the cloud.

-  Lower costs

   Cloud resource pools can quickly be scaled out to handle traffic bursts. Manual operations are no longer needed and you can save big.

Related Services
----------------

CCE integrates with other services, including Elastic Cloud Server (ECS), Virtual Private Network (VPN), and SoftWare Repository for Container (SWR).


.. figure:: /_static/images/en-us_image_0000002516077255.png
   :alt: **Figure 1** How hybrid cloud works

   **Figure 1** How hybrid cloud works
