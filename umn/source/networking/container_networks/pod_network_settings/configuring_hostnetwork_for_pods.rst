:original_name: cce_10_0402.html

.. _cce_10_0402:

Configuring hostNetwork for Pods
================================

Generally, containers in a pod use the network set up using Kubernetes network plugins. These plugins ensure network communications between pods. However, you may need to use the host (node) network of a pod. In this case, you can add **hostNetwork: true** to the pod definition.

Using the host network can reduce network forwarding overhead and improve network performance. However, there may be conflicts if the pod directly uses the host port. For details, see :ref:`Precautions <cce_10_0402__section16111716174014>`.

Configuration
-------------

Add **hostNetwork: true** to the pod definition.

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Create the **host-network.yaml** file. The following shows an example:

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

#. Create a pod that uses the host network.

   .. code-block::

      kubectl apply -f host-network.yaml

#. View the pod IP address. If the pod IP address is the same as the node IP address, the configuration succeeds.

   .. code-block::

      kubectl get pod -owide

   Information similar to the following is displayed:

   .. code-block::

      NAME                    READY   STATUS    RESTARTS   AGE     IP          NODE        NOMINATED NODE   READINESS GATES
      nginx-6fdf99c8b-6wwft   1/1     Running   0          3m41s   10.1.0.55   10.1.0.55   <none>           <none>

.. _cce_10_0402__section16111716174014:

Precautions
-----------

When a pod uses a host network, it uses a host port, and its IP address is the same as the host IP address. Before using a host network, verify that the pod port does not conflict with any service port on the host. Use a host network only if a workload pod must access a specific host port.

When using the host network, you access a pod on a node through a node port. To ensure successful access, you need to configure the security group to allow traffic from and to the node port.

In addition, using the host network requires you to reserve host ports for the pods. When using a Deployment to deploy hostNetwork pods, ensure that **the number of pods does not exceed the number of nodes**. Otherwise, multiple pods will be scheduled onto the node, and they will fail to start due to port conflicts. For example, in the preceding example **nginx** YAML, if two pods (setting **replicas** to **2**) are deployed in a cluster with only one node, one pod cannot be created. The pod logs will show that the Nginx cannot be started because the port is occupied.

.. caution::

   Do not schedule multiple pods that use the host network on the same node. Otherwise, when a ClusterIP Service is created to access a pod, the cluster IP address cannot be accessed.

To locate the fault, take the following steps:

#. Check the workload status.

   .. code-block::

      kubectl get deploy

   If the following information is displayed, one pod cannot be started.

   .. code-block::

      NAME    READY   UP-TO-DATE   AVAILABLE   AGE
      nginx   1/2     2            1           67m

#. Check the pod status.

   .. code-block::

      kubectl get pod

   In the command output, the pod is in the **CrashLoopBackOff** state.

   .. code-block::

      NAME                    READY   STATUS             RESTARTS   AGE
      nginx-6fdf99c8b-6wwft   1/1     Running            0          67m
      nginx-6fdf99c8b-rglm7   0/1     CrashLoopBackOff   13         44m

#. View the pod log.

   .. code-block::

      kubectl logs nginx-6fdf99c8b-rglm7

   The log contains the information "bind() to 0.0.0.0:80 failed (98: Address in use)", indicating that the port is occupied.

   .. code-block::

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
