:original_name: cce_10_0496.html

.. _cce_10_0496:

Mounting of a Sock File on a Node
=================================

Check Items
-----------

Check whether the **docker/containerd.sock** file is directly mounted to the pods on a node. During an upgrade, Docker or containerd restarts and the sock file on the host changes, but the sock file mounted to pods does not change accordingly. As a result, your services cannot access Docker or containerd due to sock file inconsistency. After the pods are rebuilt, the sock file is mounted to the pods again, and the issue is resolved accordingly.

Kubernetes cluster users typically use sock files in the following scenarios:

#. Monitoring applications deployed as DaemonSets use a sock file to access Docker or containerd to obtain pod statuses on a node.
#. Compilation platform applications use a sock file to access Docker or containerd to obtain containers for compiling programs.

Solution
--------

-  Scenario 1: This issue occurred on an application, and operations need to be taken to resolve this issue.

   Mount the sock file by mounting a directory. For example, if the sock file is stored in **/var/run/docker.sock** on the host, perform the following operations to resolve this issue (the following modifications will lead to the rebuilding of pods):

   .. code-block::

      kind: Deployment
      apiVersion: apps/v1
      metadata:
        name: test
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: nginx
        template:
          metadata:
            app: nginx
          spec:
            containers:
              - name: container-1
                image: 'nginx'
                imagePullPolicy: IfNotPresent
                volumeMounts:
                  - name: sock-dir
                    mountPath: /var/run
            imagePullSecrets:
              - name: default-secret
            volumes:
              - name: sock-dir
                hostPath:
                  path: /var/run

-  Scenario 2: This issue occurred on an application, and the risk that sock cannot be accessed for a short time is acceptable.

   Skip this check item and perform the check again. After the cluster is upgraded, delete the existing pods to trigger pod rebuilding. Then, the access to sock will be recovered.

-  Scenario 3: This issue occurred on some CCE add-ons of earlier versions.

   Upgrade the CCE add-ons to the latest version.

-  Scenario 4: The "failed to execute docker ps -aq" error is displayed in the log analysis.

   This error is usually caused by a container engine exception. Submit a service ticket and contact O&M personnel.
