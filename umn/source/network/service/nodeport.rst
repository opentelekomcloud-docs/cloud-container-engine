:original_name: cce_10_0142.html

.. _cce_10_0142:

NodePort
========

Scenario
--------

A Service is exposed on each node's IP address at a static port (NodePort). When you create a NodePort Service, Kubernetes automatically allocates an internal IP address (ClusterIP) of the cluster. When clients outside the cluster access <NodeIP>:<NodePort>, the traffic will be forwarded to the target pod through the ClusterIP of the NodePort Service.


.. figure:: /_static/images/en-us_image_0000002218659818.png
   :alt: **Figure 1** NodePort access

   **Figure 1** NodePort access

Notes and Constraints
---------------------

-  By default, a NodePort Service is accessed within a VPC. To use an EIP to access a NodePort Service through public networks, bind an EIP to the node in the cluster in advance.
-  After a Service is created, if the affinity setting is switched from the cluster level to the node level, the connection tracing table will not be cleared. Do not modify the Service affinity setting after the Service is created. To modify it, create a Service again.
-  In a CCE Turbo cluster, node-level affinity is supported only when the Service backend is connected to a hostNetwork pod.
-  In VPC network mode, when container A is published through a NodePort service and the service affinity is set to the node level (that is, **externalTrafficPolicy** is set to **local**), container B deployed on the same node cannot access container A through the node IP address and NodePort service.
-  When a NodePort service is created in a cluster of v1.21.7 or later, the port on the node is not displayed using **netstat** by default. If the cluster forwarding mode is **iptables**, run the **iptables -t nat -L** command to view the port. If the cluster forwarding mode is **IPVS**, run the **ipvsadm -Ln** command to view the port.

Creating a NodePort Service
---------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Services & Ingresses**. In the upper right corner, click **Create Service**.
#. Configure intra-cluster access parameters.

   -  **Service Name**: Specify a Service name, which can be the same as the workload name.
   -  **Service Type**: Select **NodePort**.
   -  **Namespace**: namespace that the workload belongs to.
   -  **Service Affinity**: For details, see :ref:`externalTrafficPolicy (Service Affinity) <cce_10_0249__section18134208069>`.

      -  **Cluster level**: The IP addresses and access ports of all nodes in a cluster can access the workload associated with the Service. Service access will cause performance loss due to route redirection, and the source IP address of the client cannot be obtained.
      -  **Node level**: Only the IP address and access port of the node where the workload is located can access the workload associated with the Service. Service access will not cause performance loss due to route redirection, and the source IP address of the client can be obtained.

   -  **Selector**: Add a label and click **Confirm**. The Service will use this label to select pods. You can also click **Reference Workload Label** to use the label of an existing workload. In the dialog box that is displayed, select a workload and click **OK**.
   -  **IPv6**: This function is disabled by default. After this function is enabled, the cluster IP address of the Service changes to an IPv6 address. **This parameter is available only in clusters of v1.15 or later with IPv6 enabled (set during cluster creation).**
   -  **Ports**

      -  **Protocol**: protocol used by the Service.
      -  **Service Port**: port used by the Service. The port number ranges from 1 to 65535.
      -  **Container Port**: listener port of the workload. For example, Nginx uses port 80 by default.
      -  **Node Port**: You are advised to select **Auto**. You can also specify a port. The default port ranges from 30000 to 32767.

#. Click **OK**.

Using kubectl
-------------

You can configure Service access using kubectl. This section uses an Nginx workload as an example to describe how to configure a NodePort Service using kubectl.

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

#. Create and edit the **nginx-nodeport-svc.yaml** file to configure Service parameters. **nginx-nodeport-svc.yaml** is an example file name. You can rename it as needed.

   .. code-block::

      vi nginx-nodeport-svc.yaml

   File content:

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        labels:
          app: nginx
        name: nginx-nodeport
      spec:
        ports:
        - name: service
          nodePort: 30000     # Node port. The value ranges from 30000 to 32767.
          port: 8080          # Port for accessing a Service
          protocol: TCP       # Protocol used for accessing a Service. The value can be TCP or UDP.
          targetPort: 80      # Port used by a Service to access the target container. This port is closely related to the applications running in a container. In this example, the Nginx image uses port 80 by default.
        selector:             # Label selector. A Service selects a pod based on the label and forwards the requests for accessing the Service to the pod. In this example, select the pod with the app:nginx label.
          app: nginx
        type: NodePort        # Service type. NodePort indicates that the Service is accessed through a node port.

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

      kubectl create -f nginx-nodeport-svc.yaml

   If information similar to the following is displayed, the Service is being created:

   .. code-block::

      service/nginx-nodeport created

   Check the created Service.

   .. code-block::

      kubectl get svc

   If information similar to the following is displayed, the Service has been created:

   .. code-block::

      # kubectl get svc
      NAME             TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
      kubernetes       ClusterIP   10.247.0.1     <none>        443/TCP          4d8h
      nginx-nodeport   NodePort    10.247.30.40   <none>        8080:30000/TCP   18s

#. Access the Service. By default, a NodePort Service can be accessed using *IP-address-of-any-node:node-port*. Cloud servers within the same VPC or containers within the cluster can access the Service. If an EIP is bound to a node, you can also use the EIP to access the Service.

   Create a container in the cluster and access it using *IP-address-of-the-node:node-port*.

   a. Obtain the node's IP address.

      .. code-block::

         kubectl get node -owide

      Command output:

      .. code-block::

         NAME           STATUS   ROLES    AGE    INTERNAL-IP    EXTERNAL-IP   OS-IMAGE                KERNEL-VERSION                CONTAINER-RUNTIME
         10.100.0.136   Ready    <none>   152m   10.100.0.136   <none>        CentOS Linux 7 (Core)   3.10.0-1160.25.1.el7.x86_64   docker://18.9.0
         10.100.0.5     Ready    <none>   152m   10.100.0.5     <none>        CentOS Linux 7 (Core)   3.10.0-1160.25.1.el7.x86_64   docker://18.9.0

   b. Create a pod and access its container.

      .. code-block::

         kubectl run -i --tty --image nginx:alpine test --rm /bin/sh

   c. Run the **curl** command to access the Service.

      .. code-block::

         curl 10.100.0.136:30000

      Command output:

      .. code-block::

         / # <!DOCTYPE html>
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
         / #
