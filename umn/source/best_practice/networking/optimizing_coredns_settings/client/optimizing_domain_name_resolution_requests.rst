:original_name: cce_bestpractice_0348.html

.. _cce_bestpractice_0348:

Optimizing Domain Name Resolution Requests
==========================================

DNS resolution is one of the most frequent network operations in Kubernetes clusters. Based on how DNS works in Kubernetes, you can optimize domain name resolution requests in the following ways.

Using a Connection Pool
-----------------------

When a container frequently communicates with another service, you are advised to use a connection pool. A connection pool caches upstream connection information, avoiding the overhead of DNS resolution and TCP connection reestablishment for each request.

Optimizing the resolve.conf File in a Container
-----------------------------------------------

The **ndots** and **search** parameters in **resolve.conf** affect the DNS resolution efficiency. For details about these parameters, see :ref:`DNS Configuration <cce_10_0365>`.

Optimizing the Domain Name Settings
-----------------------------------

To allow a pod to access a domain name, follow these rules to minimize DNS resolution and reduce resolution latency.

#. Accessing a Service in the same namespace: Use *<service-name>*, which indicates the Service name.
#. Accessing a Service across namespaces: Use *<service-name>.<namespace-name>*. *<namespace-name>* indicates the namespace where the Service is located.
#. Accessing an external domain name: Use an FQDN domain name. This type of domain name is specified by adding a period (.) at the end of a typical domain name to avoid multiple invalid search attempts caused by search domain combination.

Using Local DNS Cache
---------------------

In large-scale clusters with high DNS resolution volume, consider caching DNS resolution results on each node. It is advised to use NodeLocal DNSCache. For details, see :ref:`Using NodeLocal DNSCache to Improve DNS Performance <cce_10_0362>`.
