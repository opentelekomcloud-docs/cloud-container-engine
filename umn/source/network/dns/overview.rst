:original_name: cce_10_0360.html

.. _cce_10_0360:

Overview
========

Introduction to CoreDNS
-----------------------

When you create a cluster, the :ref:`CoreDNS add-on <cce_10_0129>` is installed to resolve domain names in the cluster.

You can view the pod of the CoreDNS add-on in the kube-system namespace.

.. code-block::

   $ kubectl get po --namespace=kube-system
   NAME                                      READY   STATUS    RESTARTS   AGE
   coredns-7689f8bdf-295rk                   1/1     Running   0          9m11s
   coredns-7689f8bdf-h7n68                   1/1     Running   0          11m

After CoreDNS is installed, it becomes a DNS. After the Service is created, CoreDNS records the Service name and IP address. In this way, the pod can obtain the Service IP address by querying the Service name from CoreDNS.

**nginx.<namespace>.svc.cluster.local** is used to access the Service. **nginx** is the Service name, **<namespace>** is the namespace, and **svc.cluster.local** is the domain name suffix. In actual use, you can omit **<namespace>.svc.cluster.local** in the same namespace and use the ServiceName.

An advantage of using ServiceName is that you can write ServiceName into the program when developing the application. In this way, you do not need to know the IP address of a specific Service.

After CoreDNS is installed, there is also a Service in the kube-system namespace, as shown below.

.. code-block::

   $ kubectl get svc -n kube-system
   NAME               TYPE           CLUSTER-IP      EXTERNAL-IP                    PORT(S)                      AGE
   coredns            ClusterIP      10.247.3.10     <none>                         53/UDP,53/TCP,8080/TCP       13d

By default, after other pods are created, the address of the CoreDNS Service is written as the address of the domain name resolution server in the **/etc/resolv.conf** file of the pod. Create a pod and view the **/etc/resolv.conf** file as follows:

.. code-block::

   $ kubectl exec test01-6cbbf97b78-krj6h -it -- /bin/sh
   / # cat /etc/resolv.conf
   nameserver 10.247.3.10
   search default.svc.cluster.local svc.cluster.local cluster.local
   options ndots:5 timeout single-request-reopen

When a user accesses the *Service name:Port* of the Nginx pod, the IP address of the Nginx Service is resolved from CoreDNS, and then the IP address of the Nginx Service is accessed. In this way, the user can access the backend Nginx pod.


.. figure:: /_static/images/en-us_image_0000001981436773.png
   :alt: **Figure 1** Example of domain name resolution in a cluster

   **Figure 1** Example of domain name resolution in a cluster

How Does Domain Name Resolution Work in Kubernetes?
---------------------------------------------------

DNS policies can be configured for each pod. Kubernetes supports DNS policies **Default**, **ClusterFirst**, **ClusterFirstWithHostNet**, and **None**. For details, see `DNS for Services and Pods <https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/>`__. These policies are specified in the **dnsPolicy** field in the pod-specific.

-  **Default**: Pods inherit the name resolution configuration from the node that the pods run on. The custom upstream DNS server and the stub domain cannot be used together with this policy.
-  **ClusterFirst**: Any DNS query that does not match the configured cluster domain suffix, such as **www.kubernetes.io**, is forwarded to the upstream name server inherited from the node. Cluster administrators may have extra stub domains and upstream DNS servers configured.
-  **ClusterFirstWithHostNet**: For pods running with hostNetwork, set its DNS policy **ClusterFirstWithHostNet**.
-  **None**: It allows a pod to ignore DNS settings from the Kubernetes environment. All DNS settings are supposed to be provided using the **dnsPolicy** field in the pod-specific.

.. note::

   -  Clusters of Kubernetes v1.10 and later support **Default**, **ClusterFirst**, **ClusterFirstWithHostNet**, and **None**. Clusters earlier than Kubernetes v1.10 support only **Default**, **ClusterFirst**, and **ClusterFirstWithHostNet**.
   -  **Default** is not the default DNS policy. If **dnsPolicy** is not explicitly specified, **ClusterFirst** is used.

**Routing**

**Without stub domain configurations**: Any query that does not match the configured cluster domain suffix, such as **www.kubernetes.io**, is forwarded to the upstream DNS server inherited from the node.

**With stub domain configurations**: If stub domains and upstream DNS servers are configured, DNS queries are routed according to the following flow:

#. The query is first sent to the DNS caching layer in CoreDNS.
#. From the caching layer, the suffix of the request is examined and then the request is forwarded to the corresponding DNS:

   -  Names with the cluster suffix, for example, **.cluster.local**: The request is sent to CoreDNS.

   -  Names with the stub domain suffix, for example, **.acme.local**: The request is sent to the configured custom DNS resolver that listens, for example, on 1.2.3.4.
   -  Names that do not match the suffix (for example, **widget.com**): The request is forwarded to the upstream DNS.


.. figure:: /_static/images/en-us_image_0000001981276629.png
   :alt: **Figure 2** Routing

   **Figure 2** Routing

Related Operations
------------------

You can also configure DNS in a workload. For details, see :ref:`DNS Configuration <cce_10_0365>`.

You can also use CoreDNS to implement user-defined domain name resolution. For details, see :ref:`Using CoreDNS for Custom Domain Name Resolution <cce_10_0361>`.
