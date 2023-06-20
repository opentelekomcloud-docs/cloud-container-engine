:original_name: cce_10_0011.html

.. _cce_10_0011:

Intra-Cluster Access (ClusterIP)
================================

Scenario
--------

ClusterIP Services allow workloads in the same cluster to use their cluster-internal domain names to access each other.

The cluster-internal domain name format is *<Service name>*.\ *<Namespace of the workload>*\ **.svc.cluster.local:**\ *<Port>*, for example, **nginx.default.svc.cluster.local:80**.

:ref:`Figure 1 <cce_10_0011__fig192245420557>` shows the mapping relationships between access channels, container ports, and access ports.

.. _cce_10_0011__fig192245420557:

.. figure:: /_static/images/en-us_image_0000001569023045.png
   :alt: **Figure 1** Intra-cluster access (ClusterIP)

   **Figure 1** Intra-cluster access (ClusterIP)

Creating a ClusterIP Service
----------------------------

#. Log in to the CCE console and access the cluster console.
#. Choose **Networking** in the navigation pane and click **Create Service** in the upper right corner.
#. Set intra-cluster access parameters.

   -  **Service Name**: Service name, which can be the same as the workload name.
   -  **Service Type**: Select **ClusterIP**.
   -  **Namespace**: Namespace to which the workload belongs.
   -  **Selector**: Add a label and click **Add**. A Service selects a pod based on the added label. You can also click **Reference Workload Label** to reference the label of an existing workload. In the dialog box that is displayed, select a workload and click **OK**.
   -  **Port Settings**

      -  **Protocol**: protocol used by the Service.
      -  **Service Port**: port used by the Service. The port number ranges from 1 to 65535.
      -  **Container Port**: port on which the workload listens. For example, Nginx uses port 80 by default.

#. Click **OK**.

Setting the Access Type Using kubectl
-------------------------------------

You can run kubectl commands to set the access type (Service). This section uses a Nginx workload as an example to describe how to implement intra-cluster access using kubectl.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create and edit the **nginx-deployment.yaml** and **nginx-clusterip-svc.yaml** files.

   The file names are user-defined. **nginx-deployment.yaml** and **nginx-clusterip-svc.yaml** are merely example file names.

   **vi nginx-deployment.yaml**

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
            containers:
            - image: nginx:latest
              name: nginx
            imagePullSecrets:
            - name: default-secret

   **vi nginx-clusterip-svc.yaml**

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        labels:
          app: nginx
        name: nginx-clusterip
      spec:
        ports:
        - name: service0
          port: 8080                # Port for accessing a Service.
          protocol: TCP             # Protocol used for accessing a Service. The value can be TCP or UDP.
          targetPort: 80            # Port used by a Service to access the target container. This port is closely related to the applications running in a container. In this example, the Nginx image uses port 80 by default.
        selector:                   # Label selector. A Service selects a pod based on the label and forwards the requests for accessing the Service to the pod. In this example, select the pod with the app:nginx label.
          app: nginx
        type: ClusterIP             # Type of a Service. ClusterIP indicates that a Service is only reachable from within the cluster.

#. Create a workload.

   **kubectl create -f nginx-deployment.yaml**

   If information similar to the following is displayed, the workload has been created.

   .. code-block::

      deployment "nginx" created

   **kubectl get po**

   If information similar to the following is displayed, the workload is running.

   .. code-block::

      NAME                     READY     STATUS             RESTARTS   AGE
      nginx-2601814895-znhbr   1/1       Running            0          15s

#. Create a Service.

   **kubectl create -f nginx-clusterip-svc.yaml**

   If information similar to the following is displayed, the Service is being created.

   .. code-block::

      service "nginx-clusterip" created

   **kubectl get svc**

   If information similar to the following is displayed, the Service has been created, and a cluster-internal IP address has been assigned to the Service.

   .. code-block::

      # kubectl get svc
      NAME              TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
      kubernetes        ClusterIP   10.247.0.1     <none>        443/TCP    4d6h
      nginx-clusterip   ClusterIP   10.247.74.52   <none>        8080/TCP   14m

#. Access a Service.

   A Service can be accessed from containers or nodes in a cluster.

   Create a pod, access the pod, and run the **curl** command to access *IP address:Port* or the domain name of the Service, as shown in the following figure.

   The domain name suffix can be omitted. In the same namespace, you can directly use **nginx-clusterip:8080** for access. In other namespaces, you can use **nginx-clusterip.default:8080** for access.

   .. code-block::

      # kubectl run -i --tty --image nginx:alpine test --rm /bin/sh
      If you do not see a command prompt, try pressing Enter.
      / # curl 10.247.74.52:8080
      <!DOCTYPE html>
      <html>
      <head>
      <title>Welcome to nginx!</title>
      <style>
          body {
              width: 35em;
              margin: 0 auto;
              font-family: Tahoma, Verdana, Arial, sans-serif;
          }
      </style>
      </head>
      <body>
      <h1>Welcome to nginx!</h1>
      <p>If you see this page, the nginx web server is successfully installed and
      working. Further configuration is required.</p>

      <p>For online documentation and support please refer to
      <a href="http://nginx.org/">nginx.org</a>.<br/>
      Commercial support is available at
      <a href="http://nginx.com/">nginx.com</a>.</p>

      <p><em>Thank you for using nginx.</em></p>
      </body>
      </html>
      / # curl nginx-clusterip.default.svc.cluster.local:8080
      ...
      <h1>Welcome to nginx!</h1>
      ...
      / # curl nginx-clusterip.default:8080
      ...
      <h1>Welcome to nginx!</h1>
      ...
      / # curl nginx-clusterip:8080
      ...
      <h1>Welcome to nginx!</h1>
      ...
