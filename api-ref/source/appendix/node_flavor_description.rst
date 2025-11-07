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

         ====================== ======================================
         Node Type              Flavor
         ====================== ======================================
         General computing-plus c4, c3, c7n, x1e
         General-purpose        s3, s2, s7n, x1
         Memory-optimized       m4, m3, m7n
         Disk-intensive         d2
         GPU-accelerated        pi2, p3, p2v, p2s, g6, g7, p5s, pi5e
         ====================== ======================================

-  **CCE Turbo clusters**

   -  ECS (VM):

      .. table:: **Table 2** x86 nodes

         ====================== ==============
         Node Type              Flavor
         ====================== ==============
         General computing-plus c3n, c7n, c4ne
         General computing      s7n
         Memory-optimized       m7n, m3n
         GPU-accelerated        g7, pi5e
         Container-plus         m3n
         ====================== ==============
