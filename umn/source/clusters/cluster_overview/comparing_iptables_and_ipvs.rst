:original_name: cce_10_0349.html

.. _cce_10_0349:

Comparing iptables and IPVS
===========================

kube-proxy is a key component of a Kubernetes cluster. It is responsible for load balancing and forwarding between a Service and its backend pod.

CCE supports two forwarding modes: iptables and IPVS.

-  IPVS allows higher throughput and faster forwarding. This mode applies to scenarios where the cluster scale is large or the number of Services is large.
-  iptables is the traditional kube-proxy mode. This mode applies to the scenario where the number of Services is small or a large number of short connections are concurrently sent on the client.

Notes and Constraints
---------------------

In a cluster using the IPVS proxy mode, if the ingress and Service use the same ELB load balancer, the ingress cannot be accessed from the nodes and containers in the cluster because kube-proxy mounts the LoadBalancer Service address to the ipvs-0 bridge. This bridge intercepts the traffic of the load balancer connected to the ingress. You are advised to use different ELB load balancers for the ingress and Service.

iptables
--------

iptables is a Linux kernel function that provides a large amount of data packet processing and filtering capabilities. It allows flexible sequences of rules to be attached to various hooks in the packet processing pipeline. When iptables is used, kube-proxy implements NAT and load balancing in the NAT pre-routing hook.

kube-proxy is an O(n) algorithm, in which *n* increases with the cluster scale. The cluster scale refers to the number of Services and backend pods.

IPVS
----

IP Virtual Server (IPVS) is constructed on top of Netfilter and implements transport-layer load balancing as part of the Linux kernel. IPVS can direct requests for TCP- and UDP-based services to the real servers, and make services of the real servers appear as virtual services on a single IP address.

In the IPVS mode, kube-proxy uses IPVS load balancing instead of iptables. IPVS is designed to balance loads for a large number of Services. It has a set of optimized APIs and uses optimized search algorithms instead of simply searching for rules from a list.

The complexity of the connection process of IPVS-based kube-proxy is O(1). In other words, in most cases, the connection processing efficiency is irrelevant to the cluster scale.

IPVS involves multiple load balancing algorithms, such as round-robin, shortest expected delay, least connections, and various hashing methods. However, iptables has only one algorithm for random selection.

Compared with iptables, IPVS has the following advantages:

#. Provides better scalability and performance for large clusters.
#. Supports better load balancing algorithms than iptables.
#. Supports functions including server health check and connection retries.
