:original_name: cce_bestpractice_0346.html

.. _cce_bestpractice_0346:

Overview
========

Application Scenarios
---------------------

DNS is one of the important basic services in Kubernetes. When the container DNS policy is not properly configured and the cluster scale is large, DNS resolution may time out or fail. In extreme cases, a large number of services in the cluster may fail to be resolved. This section describes the best practices of CoreDNS configuration optimization in Kubernetes clusters to help you avoid such problems.

Solution
--------

CoreDNS configuration optimization includes clients and servers.

On the client, you can optimize domain name resolution requests to reduce resolution latency, and use proper container images and NodeLocal DNSCache to reduce resolution exceptions.

-  :ref:`Optimizing Domain Name Resolution Requests <cce_bestpractice_0348>`
-  :ref:`Selecting a Proper Image <cce_bestpractice_0349>`
-  :ref:`Avoiding Occasional DNS Resolution Timeout Caused by IPVS Defects <cce_bestpractice_0350>`
-  :ref:`Upgrading the CoreDNS in the Cluster Timely <cce_bestpractice_0352>`
-  :ref:`Adjusting the DNS Configuration of the VPC and VM <cce_bestpractice_0353>`

On the server, you can adjust the CoreDNS deployment status or CoreDNS configuration to improve the availability and throughput of CoreDNS in the cluster.

-  :ref:`Monitoring the coredns Add-on <cce_bestpractice_0355>`
-  :ref:`Adjusting the CoreDNS Deployment Status <cce_bestpractice_0356>`

-  :ref:`Configuring CoreDNS <cce_bestpractice_0357>`

For more information about CoreDNS configurations, see https://coredns.io/.

CoreDNS open source community: https://github.com/coredns/coredns

Prerequisites
-------------

-  A CCE cluster has been created.
-  You can access the cluster using kubectl.
-  The CoreDNS add-on is installed in the cluster. The latest version of CoreDNS is recommended.
