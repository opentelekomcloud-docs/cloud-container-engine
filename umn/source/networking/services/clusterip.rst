:original_name: cce_10_0011.html

.. _cce_10_0011:

ClusterIP
=========

ClusterIP is the default Service type of Kubernetes and provides stable intra-cluster access. Kubernetes assigns a virtual IP address (cluster-scoped IP address) that can only be accessed within the cluster from the Service CIDR block of the cluster. CoreDNS maps the **cluster-internal domain name** to the assigned cluster IP address. The domain name format is *<Service-name>*.\ *<namespace-of-the-workload>*\ **.svc.cluster.local:**\ *<port>*, for example, **nginx.default.svc.cluster.local:80**.

If pods need to communicate with each other within a cluster, you can create a ClusterIP Service. For example, if a frontend pod in a cluster needs to access a backend database in the same cluster, you can create a ClusterIP Service.

:ref:`Figure 1 <cce_10_0011__fig192245420557>` shows how ClusterIP works. You can learn about the access channel, container port, and access port mapping rules of this type of Service.

.. _cce_10_0011__fig192245420557:

.. figure:: /_static/images/en-us_image_0000002434081104.png
   :alt: **Figure 1** Intra-cluster access (ClusterIP)

   **Figure 1** Intra-cluster access (ClusterIP)

Using the CCE Console
---------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Services & Ingresses**. In the upper right corner, click **Create Service**.
#. Configure intra-cluster access parameters.

   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter                         | Description                                                                                                                                                                                                                                             |
   +===================================+=========================================================================================================================================================================================================================================================+
   | Service Name                      | Enter a name, which can be the same as the workload name.                                                                                                                                                                                               |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Service Type                      | Select **ClusterIP**.                                                                                                                                                                                                                                   |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Namespace                         | Select the namespace that the workload belongs to.                                                                                                                                                                                                      |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Selector                          | The Service will be associated with the workload pods based on the label and direct traffic to the pods with this label.                                                                                                                                |
   |                                   |                                                                                                                                                                                                                                                         |
   |                                   | You can add a key and value for the pod label and click **Confirm**.                                                                                                                                                                                    |
   |                                   |                                                                                                                                                                                                                                                         |
   |                                   | You can also click **Reference Workload Label** to use the label of an existing workload. In the dialog box displayed, select a workload and click **OK**.                                                                                              |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Protocol Version                  | Select the IP address of different versions based on service requirements. **This function is displayed only when IPv6 is enabled during the creation of clusters of v1.15 or later.**                                                                  |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Port                              | -  **Protocol**: the protocol supported by the Service.                                                                                                                                                                                                 |
   |                                   | -  **Container Port**: the listening port of the service containers. The port ranges from 1 to 65535. You need to determine the port based on the container image. For example, the default port of Nginx is 80, and the default port of MySQL is 3306. |
   |                                   | -  **Service Port**: the port used to access the ClusterIP Service. You can customize the port as required. The port ranges from 1 to 65535.                                                                                                            |
   +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **OK**. Then, access the Service through *<ClusterIP>*:*<Service-port>*.

Using kubectl
-------------

You can configure Service access using kubectl. This section uses an Nginx workload as an example to describe how to implement intra-cluster access using kubectl.

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Create and edit the **nginx-deployment.yaml** file to configure the sample workload. For details, see :ref:`Creating a Deployment <cce_10_0047>`. **nginx-deployment.yaml** is an example file name. You can rename it as needed.

   .. code-block::

      vi nginx-deployment.yaml

   File content:

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

#. Create and edit the **nginx-clusterip-svc.yaml** file to configure Service parameters. **nginx-clusterip-svc.yaml** is an example file name. You can rename it as needed.

   .. code-block::

      vi nginx-clusterip-svc.yaml

   File content:

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
          port: 8080                # Port for accessing a Service
          protocol: TCP             # Protocol used for accessing a Service. The value can be TCP or UDP.
          targetPort: 80            # Port used by a Service to access the target container. This port is closely related to the applications running in a container. In this example, the Nginx image uses port 80 by default.
        selector:                   # Label selector. A Service selects a pod based on the label and forwards the requests for accessing the Service to the pod. In this example, select the pod with the app:nginx label.
          app: nginx
        type: ClusterIP             # Type of a Service. ClusterIP indicates that a Service is only reachable from within the cluster.

#. Create a workload.

   .. code-block::

      kubectl create -f nginx-deployment.yaml

   If information similar to the following is displayed, the workload has been created:

   .. code-block::

      deployment/nginx created

   Check the created workload.

   .. code-block::

      kubectl get pod

   If information similar to the following is displayed, the workload is running:

   .. code-block::

      NAME                     READY     STATUS             RESTARTS   AGE
      nginx-2601814895-znhbr   1/1       Running            0          15s

#. Create a Service.

   .. code-block::

      kubectl create -f nginx-clusterip-svc.yaml

   If information similar to the following is displayed, the Service is being created:

   .. code-block::

      service/nginx-clusterip created

   Check the created Service.

   .. code-block::

      kubectl get svc

   If information similar to the following is displayed, the Service has been created, and a cluster-internal IP address has been assigned to the Service.

   .. code-block::

      NAME              TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
      kubernetes        ClusterIP   10.247.0.1     <none>        443/TCP    4d6h
      nginx-clusterip   ClusterIP   10.247.74.52   <none>        8080/TCP   14m

#. Access the Service from a container or node in the cluster.

   a. Create a pod and access its container.

      .. code-block::

         kubectl run -i --tty --image nginx:alpine test --rm /bin/sh

   b. Run the **curl** command to access the Service.

      -  Access through *IP:Port*:

         .. code-block::

            curl 10.247.74.52:8080

      -  Access using *Domain name:Port* (not supported on nodes):

         .. code-block::

            curl nginx-clusterip.default.svc.cluster.local:8080

         *nginx-clusterip* is the Service name, *default* is the namespace where the Service is located, and *svc.cluster.local* is the DNS domain for the ClusterIP Service.

         You can simplify the domain name based on your requirements. For example, if the Service and the accessing pod are in the same namespace, you can use **nginx-clusterip:8080** to access it. If they are in different namespaces, you can use **nginx-clusterip.default:8080** to access it.

      If the access is successful, the following information will be displayed:

      .. code-block::

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
