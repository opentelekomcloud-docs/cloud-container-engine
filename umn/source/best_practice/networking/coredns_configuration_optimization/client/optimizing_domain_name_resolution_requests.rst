:original_name: cce_bestpractice_0348.html

.. _cce_bestpractice_0348:

Optimizing Domain Name Resolution Requests
==========================================

DNS resolution is frequently used in Kubernetes clusters. Based on the characteristics of DNS resolution in Kubernetes, you can optimize domain name resolution requests in the following ways.

Using a Connection Pool
-----------------------

When a containerized application needs to frequently request another service, you are advised to use a connection pool. The connection pool can cache the link information of the upstream service to avoid the overhead of DNS resolution and TCP link reestablishment for each access.

Optimizing the resolve.conf File in the Container
-------------------------------------------------

The settings of **ndots** and **search** in **resolve.conf** determine the domain name resolution efficiency. For details about the two parameters, see :ref:`DNS Configuration <cce_10_0365>`.

Optimizing the Domain Name Configuration
----------------------------------------

When a container needs to access a domain name, configure the domain name based on the following rules to improve the domain name resolution efficiency.

#. When a pod accesses a Service in the same namespace, use *<service-name>*, which indicates the Service name.
#. When a pod accesses a Service across namespaces, use *<service-name>.<namespace-name>*. *<namespace-name>* indicates the namespace where the Service is located.
#. When a pod accesses a domain name outside the cluster, it uses the FQDN domain name. This type of domain name is specified by adding a period (.) at the end of a typical domain name to avoid multiple invalid search attempts caused by search domain combination.

Using Local Cache
-----------------

If the cluster specifications are large and the number of DNS resolution requests is large, you can cache the DNS resolution result on the node. You are advised to use NodeLocal DNSCache. For details, see :ref:`Using NodeLocal DNSCache to Improve DNS Performance <cce_10_0362>`.
