:original_name: cce_bestpractice_0355.html

.. _cce_bestpractice_0355:

Monitoring the coredns Add-on
=============================

-  CoreDNS exposes health metrics such as resolution results through the standard Prometheus API to detect exceptions on the CoreDNS server or even upstream DNS server.
-  Port for obtaining coredns metrics. The default zone listening IP address is **{$\ POD_IP}:9153**. Retain the default value. Otherwise, Prometheus cannot collect the metrics.
-  If you use on-premises Prometheus to monitor Kubernetes clusters, you can observe related metrics on Prometheus and configure alarms for the key metrics. For details, see `prometheus <https://coredns.io/plugins/metrics/>`__.
