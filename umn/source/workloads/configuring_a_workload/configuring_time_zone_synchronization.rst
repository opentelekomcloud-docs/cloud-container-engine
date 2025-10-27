:original_name: cce_10_0354.html

.. _cce_10_0354:

Configuring Time Zone Synchronization
=====================================

When creating a workload, you can enable time zone synchronization, so that the container can use the same time zone as the node.

Enabling Time Zone Synchronization in One Click on the Console
--------------------------------------------------------------

#. Log in to the CCE console.
#. Click the cluster name to go to the cluster console, choose **Workloads** in the navigation pane, and click the **Create Workload** in the upper right corner.
#. In the **Basic Info** area of the **Create Workload** page, enable **Time Zone Synchronization** so that the same time zone will be used for both the container and node.
#. Complete other settings and click **Create Workload**.

Manually Mounting a Time Zone Configuration File on the Console
---------------------------------------------------------------

#. Log in to the CCE console.

#. Click the cluster name to go to the cluster console, choose **Workloads** in the navigation pane, and click the **Create Workload** in the upper right corner.

#. Click **Data Storage** in the **Container Information** area under **Container Settings** and choose **Add Volume** > **hostPath**.

   Configure the following parameters:

   -  **hostPath**: **/etc/localtime**
   -  **Mount Path**: **/etc/localtime**
   -  **Permission**: Read-only

#. Complete other settings and click **Create Workload**.

Manually Mounting a Time Zone Configuration File Using YAML
-----------------------------------------------------------

To use time zone synchronization, you need to mount **/etc/localtime** on the node to **/etc/localtime** in the container. In this way, the node and container use the same time zone configuration file.

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Create a file named **nginx-deployment.yaml**. The name is only an example. You can rename the file as needed.

   .. code-block::

      vi nginx-deployment.yaml

   Example YAML:

   .. code-block::

      kind: Deployment
      apiVersion: apps/v1
      metadata:
        name: test
        namespace: default
      spec:
        replicas: 2
        selector:
          matchLabels:
            app: test
        template:
          metadata:
            labels:
              app: test
          spec:
            volumes:
              - name: date-config  # Custom volume name.
                hostPath:   # Mount the time zone configuration file of the node in hostPath mode.
                  path: /etc/localtime  # Path of the time zone configuration file on the node.
            containers:
              - name: container-0
                image: 'nginx:alpine'
                volumeMounts:
                  - name: date-config  # Mount the volume to the container. The volume name must be the same as that you defined.
                    readOnly: true # Mount the volume as read-only.
                    mountPath: /etc/localtime # The path in the container where the node's time zone configuration file is mounted.
                imagePullPolicy: IfNotPresent
            imagePullSecrets:
              - name: default-secret

   Press **Esc** and enter **:wq** to save the file and exit.

#. Create the workload.

   .. code-block::

      kubectl create -f nginx-deployment.yaml

Verifying that the Container and Node Are in the Same Time Zone
---------------------------------------------------------------

#. Log in to the node and obtain the time zone.

   .. code-block::

      date -R

   Information similar to the following is displayed:

   .. code-block::

      Sat, 12 Apr 2025 16:58:47 +0800

#. Check whether the time zone of the container is the same as that of the node.

   Obtain the pod name.

   .. code-block::

      kubectl get pod

   Access the container. In the command, *<pod_name>* specifies the pod name obtained in the previous step.

   .. code-block::

      kubectl exec -it <pod_name> -- /bin/bash

   Obtain the time zone of the container.

   .. code-block::

      date -R

   Information similar to the following is displayed, indicating that the time zone of the container is the same as that of the node:

   .. code-block::

      Sat, 12 Apr 2025 16:59:23 +0800
