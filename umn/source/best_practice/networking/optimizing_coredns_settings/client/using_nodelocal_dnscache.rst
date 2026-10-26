:original_name: cce_bestpractice_0351.html

.. _cce_bestpractice_0351:

Using NodeLocal DNSCache
========================

Challenges
----------

When the number of DNS requests in a cluster increases, the load of CoreDNS increases and the following issues may occur:

-  Increased delay: CoreDNS needs to process more requests, which may slow down the DNS query and affect service performance.
-  Increased resource usage: To ensure DNS performance, CoreDNS requires higher specifications.

Solution
--------

NodeLocal DNSCache can improve the stability and performance of service discovery.

For details about NodeLocal DNSCache and how to deploy it in a cluster, see :ref:`Using NodeLocal DNSCache to Improve DNS Performance <cce_10_0362>`.
