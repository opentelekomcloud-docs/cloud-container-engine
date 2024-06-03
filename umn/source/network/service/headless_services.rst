:original_name: cce_10_0398.html

.. _cce_10_0398:

Headless Services
=================

Services allow internal and external pod access, but not the following scenarios:

-  Accessing all pods at the same time
-  Pods in a Service accessing each other

This is where headless Service come into service. A headless Service does not create a cluster IP address, and the DNS records of all pods are returned during query. In this way, the IP addresses of all pods can be queried. :ref:`StatefulSets <cce_10_0048>` use headless Services to support mutual access between pods.

.. code-block::

   apiVersion: v1
   kind: Service       # Object type (Service)
   metadata:
     name: nginx-headless
     labels:
       app: nginx
   spec:
     ports:
       - name: nginx      #     - name: nginx     # Name of the port for communication between pods
         port: 80        # Port number for communication between pods
     selector:
       app: nginx        # Select the pod whose label is app:nginx.
     clusterIP: None     # Set this parameter to None, indicating that a headless Service is to be created.

Run the following command to create a headless Service:

.. code-block::

   # kubectl create -f headless.yaml
   service/nginx-headless created

After the Service is created, you can query the Service.

.. code-block::

   # kubectl get svc
   NAME             TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
   nginx-headless   ClusterIP   None         <none>        80/TCP    5s

Create a pod to query the DNS. You can view the records of all pods. In this way, all pods can be accessed.

.. code-block::

   $ kubectl run -i --tty --image tutum/dnsutils dnsutils --restart=Never --rm /bin/sh
   If you do not see a command prompt, try pressing Enter.
   / # nslookup nginx-0.nginx
   Server:         10.247.3.10
   Address:        10.247.3.10#53
   Name:   nginx-0.nginx.default.svc.cluster.local
   Address: 172.16.0.31

   / # nslookup nginx-1.nginx
   Server:         10.247.3.10
   Address:        10.247.3.10#53
   Name:   nginx-1.nginx.default.svc.cluster.local
   Address: 172.16.0.18

   / # nslookup nginx-2.nginx
   Server:         10.247.3.10
   Address:        10.247.3.10#53
   Name:   nginx-2.nginx.default.svc.cluster.local
   Address: 172.16.0.19
