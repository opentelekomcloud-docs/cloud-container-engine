:original_name: cce_bestpractice_00227.html

.. _cce_bestpractice_00227:

Modifying Kernel Parameters Using a Privileged Container
========================================================

Prerequisites
-------------

To access a Kubernetes cluster from a client, you can use the Kubernetes command line tool kubectl.

Procedure
---------

#. Create a DaemonSet in the background, select the Nginx image, enable the Privileged Container, configure the lifecycle, and add the **hostNetwork** field (value: **true**).

   a. Create a **daemonSet** file.

      **vi daemonSet.yaml**

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

   b. Create a DaemonSet.

      **kubectl create -f daemonSet.yaml**

#. Check whether the DaemonSet is successfully created.

   **kubectl get daemonset** *DaemonSet name*

   In this example, run the following command:

   **kubectl get daemonset** **daemonset-test**

   Information similar to the following is displayed:

   .. code-block::

      NAME               DESIRED    CURRENT   READY    UP-T0-DATE    AVAILABLE     NODE SELECTOR   AGE
      daemonset-test     2          2         2        2             2             <node>          2h

#. Query the container ID of DaemonSet on the node.

   **docker ps -a|grep** *DaemonSet name*

   In this example, run the following command:

   **docker ps -a|grep** **daemonset-test**

   Information similar to the following is displayed:

   .. code-block::

      897b99faa9ce        3e094d5696c1                           "/bin/sh  -c while..."     31 minutes ago     Up  30 minutes  ault_fa7cc313-4ac1-11e9-a716-fa163e0aalba_0

#. Access the container.

   **docker exec -it** *containerid* **/bin/sh**

   In this example, run the following command:

   **docker exec -it** *897b99faa9ce* **/bin/sh**

#. Check whether the configured command is executed after the container is started.

   **sysctl -a \|grep net.ipv4.tcp_tw_reuse**

   If the following information is displayed, the system parameters are modified successfully:

   .. code-block::

      net.ipv4.tcp_tw_reuse=1
