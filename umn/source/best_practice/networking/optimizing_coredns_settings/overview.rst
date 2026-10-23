:original_name: cce_bestpractice_0346.html

.. _cce_bestpractice_0346:

Overview
========

Application Scenarios
---------------------

DNS is a critical foundational service in Kubernetes. When DNS policies are misconfigured, especially in large-scale clusters, DNS resolution may time out or fail. In severe cases, a large number of services in the cluster may fail to be resolved. This section describes the best practices for optimizing CoreDNS in Kubernetes clusters to help prevent these issues.

Solution
--------

CoreDNS optimization covers both client-side and server-side improvements.

On the client side, you can optimize domain name resolution requests to reduce latency, use appropriate container images, and use NodeLocal DNSCache to reduce resolution failures.

-  :ref:`Optimizing Domain Name Resolution Requests <cce_bestpractice_0348>`
-  :ref:`Selecting a Proper Image <cce_bestpractice_0349>`
-  :ref:`Avoiding Occasional DNS Resolution Timeout Caused by IPVS Defects <cce_bestpractice_0350>`
-  :ref:`Using NodeLocal DNSCache <cce_bestpractice_0351>`
-  :ref:`Keeping CoreDNS Up to Date <cce_bestpractice_0352>`
-  :ref:`Adjusting the DNS Settings of the VPC and VM <cce_bestpractice_0353>`

On the server side, you can adjust the CoreDNS deployment or fine-tune its settings to improve availability and throughput.

-  :ref:`Monitoring CoreDNS <cce_bestpractice_0355>`
-  :ref:`Adjusting the CoreDNS Deployment Status <cce_bestpractice_0356>`

-  :ref:`Properly Configuring CoreDNS <cce_bestpractice_0357>`

For details about CoreDNS settings, see https://coredns.io/.

CoreDNS open-source community: https://github.com/coredns/coredns

Prerequisites
-------------

-  A CCE cluster is available.
-  You can access the cluster using kubectl.
-  The CoreDNS add-on is installed in the cluster. The latest version of CoreDNS is recommended.
