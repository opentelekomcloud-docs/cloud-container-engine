:original_name: cce_productdesc_0012.html

.. _cce_productdesc_0012:

Regions and AZs
===============

Definition
----------

A region and availability zone (AZ) identify the location of a data center. You can create resources in a specific region and AZ.

-  Regions are divided based on geographical location and network latency. Public services, such as Elastic Cloud Server (ECS), Elastic Volume Service (EVS), Object Storage Service (OBS), Virtual Private Cloud (VPC), Elastic IP (EIP), and Image Management Service (IMS), are shared within the same region. Regions are classified as universal regions and dedicated regions. A universal region provides universal cloud services for common domains. A dedicated region provides services of the same type only or for specific domains.
-  An AZ contains one or more physical data centers. Each AZ has independent cooling, fire extinguishing, moisture-proof, and electricity facilities. Within an AZ, computing, network, storage, and other resources are logically divided into multiple clusters. AZs in a region are interconnected through high-speed optic fibers. This is helpful if you will deploy systems across AZs to achieve higher availability.

Cloud services are available in many regions around the world. You can select a region and AZ as needed.

How to Select a Region?
-----------------------

When selecting a region, consider the following factors:

-  Location

   Select a region close to you or your target users to reduce network latency and improve access rate.

Selecting an AZ
---------------

When deploying resources, consider your applications' requirements on disaster recovery (DR) and network latency.

-  For high DR capability, deploy resources in different AZs within the same region.
-  For lower network latency, deploy resources in the same AZ.

Regions and Endpoints
---------------------

When using an API to access resources, you must specify a region and endpoint. For more information about regions and endpoints, see `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.
