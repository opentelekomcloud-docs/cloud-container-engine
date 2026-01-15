:original_name: cce_bestpractice_00227.html

.. _cce_bestpractice_00227:

Modifying Kernel Parameters Using a Privileged Container
========================================================

Prerequisites
-------------

To access a Kubernetes cluster from a client, you can use the Kubernetes command line tool kubectl.

Procedure
---------

#. Create a DaemonSet on the backend, select the Nginx image, enable the privileged container, configure the lifecycle, and specify **hostNetwork: true**.

   a. Create a DaemonSet file.

      .. code-block::

         vi daemonset.yaml

      An example YAML file is provided as follows:

      .. important::

         The **spec.spec.containers.lifecycle** field indicates the command that will be run after the container is started.

      .. code-block::

         kind: DaemonSet
         apiVersion: apps/v1
         metadata:
           name: daemonset-test
           labels:
             name: daemonset-test
         spec:
           selector:
             matchLabels:
               name: daemonset-test
           template:
             metadata:
               labels:
                 name: daemonset-test
             spec:
               hostNetwork: true
               containers:
               - name: daemonset-test
                 image: nginx:alpine-perl
                 command:
                 - "/bin/sh"
                 args:
                 - "-c"
                 - while :; do  time=$(date);done
                 imagePullPolicy: IfNotPresent
                 lifecycle:
                   postStart:
                     exec:
                       command:
                       - sysctl
                       - "-w"
                       - net.ipv4.tcp_tw_reuse=1
                 securityContext:
                   privileged: true
               imagePullSecrets:
               - name: default-secret

   b. Create the DaemonSet.

      .. code-block::

         kubectl create -f daemonSet.yaml

#. Check whether the DaemonSet has been created.

   .. code-block::

      kubectl get daemonset {daemonset_name}

   In this example, run the following command:

   .. code-block::

      kubectl get daemonset daemonset-test

   Information similar to the following is displayed:

   .. code-block::

      NAME               DESIRED    CURRENT   READY    UP-TO-DATE    AVAILABLE     NODE SELECTOR   AGE
      daemonset-test     2          2         2        2             2             <node>          2h

#. Obtain the IDs of the DaemonSet pods on the nodes.

   .. code-block::

      kubectl get pod | grep {daemonset_name}

   In this example, run the following command:

   .. code-block::

      kubectl get pod | grep daemonset-test

   Information similar to the following is displayed:

   .. code-block::

      daemonset-test-mqdpv               1/1     Running             0          2h
      daemonset-test-n56vm               1/1     Running             0          2h

#. Access the container.

   .. code-block::

      kubectl exec -it {pod_name} -- /bin/sh

   In this example, run the following command:

   .. code-block::

      kubectl exec -it daemonset-test-mqdpv -- /bin/sh

#. Check whether the configured command is executed after the container is started.

   .. code-block::

      sysctl -a |grep net.ipv4.tcp_tw_reuse

   If the following information is displayed, the system parameters are modified successfully:

   .. code-block::

      net.ipv4.tcp_tw_reuse=1
