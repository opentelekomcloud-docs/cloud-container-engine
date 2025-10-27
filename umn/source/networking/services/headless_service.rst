:original_name: cce_10_0398.html

.. _cce_10_0398:

Headless Service
================

Headless Services are a special type of Kubernetes Service. They do not provide a cluster IP address or load balancing through a Service IP address. Instead, when a client uses the DNS name of a headless Service for query, Kubernetes directly returns the individual IP addresses of the pods selected by the Service. This design allows clients to connect directly to the pods themselves. Headless Services are ideal for certain applications such as database clusters and cache clusters.

Headless Services are typically used in:

-  **StatefulSets**: In StatefulSets such as MySQL, Kafka, and Elasticsearch, each pod has unique responsibilities and does not require load balancing. A headless Service gives each pod its own access entry.
-  **Applications that need custom load balancing**: Some applications need to manage their own load balancing policies rather than relying on Kubernetes' default load balancing policy.

Creating a Headless Service
---------------------------

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Create a StatefulSet and an associated headless Service.

   .. code-block::

      vi headless.yaml

   File content:

   .. code-block::

      apiVersion: v1
      kind: Service       # Set the resource object type to Service.
      metadata:
        name: nginx-headless
        labels:
          app: nginx
      spec:
        ports:
          - name: web       # Name of the port for communications between pods
            port: 80        # Port number for communications between pods
        selector:
          app: nginx        # Select the pod labeled with app:nginx.
        clusterIP: None     # Set this parameter to None, indicating that a headless Service will be created.
      ---
      apiVersion: apps/v1
      kind: StatefulSet
      metadata:
        name: web
      spec:
        serviceName: nginx-headless   # Name of the headless Service associated with the StatefulSet
        replicas: 3
        selector:
          matchLabels:
            app: nginx
        template:
          metadata:
            labels:
              app: nginx
          spec:
            containers:
            - name: nginx
              image: nginx:alpine
              ports:
              - containerPort: 80
                name: web
            imagePullSecrets:
              - name: default-secret

#. Create the StatefulSet and headless Service.

   .. code-block::

      kubectl create -f headless.yaml

#. Check whether the pods have been created.

   .. code-block::

      kubectl get pods -l app=nginx -o wide

   Information similar to the following is displayed:

   .. code-block::

      NAME     READY   STATUS    RESTARTS   AGE     IP          NODE            NOMINATED NODE   READINESS GATES
      web-0    1/1     Running   0          41s     10.0.0.22   192.168.0.194   <none>           <none>
      web-1    1/1     Running   0          38s     10.0.0.23   192.168.0.194   <none>           <none>
      web-2    1/1     Running   0          37s     10.0.0.39   192.168.0.21    <none>           <none>

#. Check whether the headless Service has been created.

   .. code-block::

      kubectl get svc nginx-headless

   Information similar to the following is displayed:

   .. code-block::

      NAME             TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
      nginx-headless   ClusterIP   None         <none>        80/TCP    2m20s

#. Create a temporary pod to obtain DNS.

   .. code-block::

      kubectl run -it --rm --image=busybox:1.28 test-pod -- sh

   The command for obtaining DNS is as follows:

   .. code-block::

      nslookup nginx-headless

   Information similar to the following is displayed:

   .. code-block::

      Server:    10.247.3.10
      Address 1: 10.247.3.10 coredns.kube-system.svc.cluster.local

      Name:      nginx-headless
      Address 1: 10.0.0.22 web-0.nginx-headless.default.svc.cluster.local
      Address 2: 10.0.0.23 web-1.nginx-headless.default.svc.cluster.local
      Address 3: 10.0.0.39 web-2.nginx-headless.default.svc.cluster.local

   This indicates that each StatefulSet pod has an independent DNS record.

#. Check whether these pods can be accessed.

   .. code-block::

      wget -qO- web-0.nginx-headless
