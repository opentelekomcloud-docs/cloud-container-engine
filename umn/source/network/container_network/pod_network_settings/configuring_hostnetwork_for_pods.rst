:original_name: cce_10_0402.html

.. _cce_10_0402:

Configuring hostNetwork for Pods
================================

Scenario
--------

Kubernetes allows pods to directly use the host/node network. When a pod is configured with **hostNetwork: true**, applications running in the pod can directly view the network interface of the host where the pod is located.

Configuration
-------------

Add **hostNetwork: true** to the pod definition.

.. code-block::

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: nginx
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: nginx
     template:
       metadata:
         labels:
           app: nginx
       spec:
         hostNetwork: true
         containers:
         - image: nginx:alpine
           name: nginx
         imagePullSecrets:
         - name: default-secret

The configuration succeeds if the pod IP is the same as the node IP.

.. code-block::

   $ kubectl get pod -owide
   NAME                    READY   STATUS    RESTARTS   AGE     IP          NODE        NOMINATED NODE   READINESS GATES
   nginx-6fdf99c8b-6wwft   1/1     Running   0          3m41s   10.1.0.55   10.1.0.55   <none>           <none>

Precautions
-----------

If a pod uses the host network, it occupies a host port. The pod IP is the host IP. To use the host network, you must confirm pods do not conflict with each other in terms of the host ports they occupy. Do not use the host network unless a specific application must use a specific port on the host.

When using the host network, you access a pod on a node through a node port. Therefore, **allow access from the security group port of the node**. Otherwise, the access fails.

In addition, using the host network requires you to reserve host ports for the pods. When using a Deployment to deploy pods of the hostNetwork type, ensure that **the number of pods does not exceed the number of nodes**. Otherwise, multiple pods will be scheduled onto the node, and they will fail to start due to port conflicts. For example, in the preceding example nginx YAML, if two pods (setting **replicas** to **2**) are deployed in a cluster with only one node, one pod cannot be created. The pod logs will show that the Nginx cannot be started because the port is occupied.

.. caution::

   Do not schedule multiple pods that use the host network on the same node. Otherwise, when a ClusterIP Service is created to access a pod, the cluster IP address cannot be accessed.

.. code-block::

   $ kubectl get deploy
   NAME    READY   UP-TO-DATE   AVAILABLE   AGE
   nginx   1/2     2            1           67m
   $ kubectl get pod
   NAME                    READY   STATUS             RESTARTS   AGE
   nginx-6fdf99c8b-6wwft   1/1     Running            0          67m
   nginx-6fdf99c8b-rglm7   0/1     CrashLoopBackOff   13         44m
   $ kubectl logs nginx-6fdf99c8b-rglm7
   /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
   /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
   /docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
   10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
   10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
   /docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
   /docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
   /docker-entrypoint.sh: Configuration complete; ready for start up
   2022/05/11 07:18:11 [emerg] 1#1: bind() to 0.0.0.0:80 failed (98: Address in use)
   nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address in use)
   2022/05/11 07:18:11 [emerg] 1#1: bind() to [::]:80 failed (98: Address in use)
   nginx: [emerg] bind() to [::]:80 failed (98: Address in use)
   2022/05/11 07:18:11 [emerg] 1#1: bind() to 0.0.0.0:80 failed (98: Address in use)
   nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address in use)
   2022/05/11 07:18:11 [emerg] 1#1: bind() to [::]:80 failed (98: Address in use)
   nginx: [emerg] bind() to [::]:80 failed (98: Address in use)
   2022/05/11 07:18:11 [emerg] 1#1: bind() to 0.0.0.0:80 failed (98: Address in use)
   nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address in use)
   2022/05/11 07:18:11 [emerg] 1#1: bind() to [::]:80 failed (98: Address in use)
   nginx: [emerg] bind() to [::]:80 failed (98: Address in use)
   2022/05/11 07:18:11 [emerg] 1#1: bind() to 0.0.0.0:80 failed (98: Address in use)
   nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address in use)
   2022/05/11 07:18:11 [emerg] 1#1: bind() to [::]:80 failed (98: Address in use)
   nginx: [emerg] bind() to [::]:80 failed (98: Address in use)
   2022/05/11 07:18:11 [emerg] 1#1: bind() to 0.0.0.0:80 failed (98: Address in use)
   nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address in use)
   2022/05/11 07:18:11 [emerg] 1#1: bind() to [::]:80 failed (98: Address in use)
   nginx: [emerg] bind() to [::]:80 failed (98: Address in use)
   2022/05/11 07:18:11 [emerg] 1#1: still could not bind()
   nginx: [emerg] still could not bind()
