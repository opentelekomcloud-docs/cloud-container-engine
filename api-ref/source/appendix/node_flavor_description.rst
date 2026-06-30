:original_name: cce_02_0368.html

.. _cce_02_0368:

Node Flavor Description
=======================

.. note::

   Different regions support different node flavors, and node flavors may be changed. You are advised to log in to the CCE console and check whether the required node flavors are supported on the page for creating nodes.

-  **CCE cluster**

   CCE clusters support only 2 vCPUs and 4 GiB or higher specifications. You are advised to query node specifications on the console. For details about node specifications, see ECS Specifications.

   You need to enter the specific flavor name, for example, **c4.large.2**.

   The following tables list the specifications supported by CCE. (The specifications of some ECS models vary by region. The actual specifications displayed on the console are used.)

   -  ECS (VM):

      .. table:: **Table 1** x86 nodes

         ====================== ==============================================
         Node Type              Flavor
         ====================== ==============================================
         General computing-plus c3, c4, c7n, c9, x1e, x2e
         General-purpose        s2, s3, s7n, s9, x1, x2
         Memory-optimized       m3, m4, m7n, m9, x1m, x2m
         Disk-intensive         d2
         GPU-accelerated        g6, g7, p2v, p2s, p3, p5e, p5s, pi2, pi5e 
         ====================== ==============================================

-  **CCE Turbo clusters**

   -  ECS (VM):

      .. table:: **Table 2** x86 nodes

         ====================== ===========================
         Node Type              Flavor
         ====================== ===========================
         General computing-plus c3n, c4ne, c7n, c9, x2ne
         General computing      s7n, s9
         Memory-optimized       m3n, m7n, m9
         GPU-accelerated        g7, pi5e, p5e
         Container-plus         m3n
         ====================== ===========================
