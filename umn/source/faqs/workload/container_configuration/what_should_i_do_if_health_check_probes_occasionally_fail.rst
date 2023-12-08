:original_name: cce_faq_00255.html

.. _cce_faq_00255:

What Should I Do If Health Check Probes Occasionally Fail?
==========================================================

When the liveness and readiness probes fail to perform the health check, locate the service fault first.

Common causes are as follows:

-  The service processing takes a long time. As a result, the response times out.
-  The Tomcat connection setup and waiting time are too long (for example, too many connections or threads). As a result, the response times out.
-  The performance of the node where the container is located, such as the disk I/O, reaches the bottleneck. As a result, the service processing times out.
