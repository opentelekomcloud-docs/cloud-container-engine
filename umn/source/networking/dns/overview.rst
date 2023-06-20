:original_name: cce_10_0360.html

.. _cce_10_0360:

Overview
========

Introduction to CoreDNS
-----------------------

When you create a cluster, the :ref:`coredns add-on <cce_10_0129>` is installed to resolve domain names in the cluster.

You can view the pod of the coredns add-on in the kube-system namespace.

.. code-block::

   $ kubectl get po --namespace=kube-system
   NAME                                      READY   STATUS    RESTARTS   AGE
   coredns-7689f8bdf-295rk                   1/1     Running   0          9m11s
   coredns-7689f8bdf-h7n68                   1/1     Running   0          11m

After coredns is installed, it becomes a DNS. After the Service is created, coredns records the Service name and IP address. In this way, the pod can obtain the Service IP address by querying the Service name from coredns.

**nginx.<namespace>.svc.cluster.local** is used to access the Service. **nginx** is the Service name, **<namespace>** is the namespace, and **svc.cluster.local** is the domain name suffix. In actual use, you can omit **<namespace>.svc.cluster.local** in the same namespace and use the ServiceName.

An advantage of using ServiceName is that you can write ServiceName into the program when developing the application. In this way, you do not need to know the IP address of a specific Service.

After the coredns add-on is installed, there is also a Service in the kube-system namespace, as shown below.

.. code-block::

   $ kubectl get svc -n kube-system
   NAME               TYPE           CLUSTER-IP      EXTERNAL-IP                    PORT(S)                      AGE
   coredns            ClusterIP      10.247.3.10     <none>                         53/UDP,53/TCP,8080/TCP       13d

By default, after other pods are created, the address of the coredns Service is written as the address of the domain name resolution server in the **/etc/resolv.conf** file of the pod. Create a pod and view the **/etc/resolv.conf** file as follows:

.. code-block::

   $ kubectl exec test01-6cbbf97b78-krj6h -it -- /bin/sh
   / # cat /etc/resolv.conf
   nameserver 10.247.3.10
   search default.svc.cluster.local svc.cluster.local cluster.local
   options ndots:5 timeout single-request-reopen

When a user accesses the *Service name:Port* of the Nginx pod, the IP address of the Nginx Service is resolved from CoreDNS, and then the IP address of the Nginx Service is accessed. In this way, the user can access the backend Nginx pod.


.. figure:: /_static/images/en-us_image_0000001568822905.png
   :alt: **Figure 1** Example of domain name resolution in a cluster

   **Figure 1** Example of domain name resolution in a cluster

Related Operations
------------------

You can also configure DNS in a workload. For details, see :ref:`DNS Configuration <cce_10_0365>`.

You can also use coredns to implement user-defined domain name resolution. For details, see :ref:`Using CoreDNS for Custom Domain Name Resolution <cce_10_0361>`.
