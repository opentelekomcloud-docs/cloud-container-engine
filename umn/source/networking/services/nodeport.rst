:original_name: cce_01_0142.html

.. _cce_01_0142:

NodePort
========

Scenario
--------

A Service is exposed on each node's IP address at a static port (NodePort). A ClusterIP Service, to which the NodePort Service will route, is automatically created. By requesting <NodeIP>:<NodePort>, you can access a NodePort Service from outside the cluster.


.. figure:: /_static/images/en-us_image_0000001163847995.png
   :alt: **Figure 1** NodePort access

   **Figure 1** NodePort access

Notes and Constraints
---------------------

-  By default, a NodePort Service is accessed within a VPC. If you need to use an EIP to access a NodePort Service through public networks, bind an EIP to the node in the cluster in advance.
-  After a Service is created, if the :ref:`affinity setting <cce_01_0142__li195904442002>` is switched from the cluster level to the node level, the connection tracing table will not be cleared. You are advised not to modify the Service affinity setting after the Service is created. If you need to modify it, create a Service again.
-  The service port of a NodePort Service created on the CCE console is the same as the configured container port.
-  CCE Turbo clusters support only cluster-level service affinity.

Adding a Service When Creating a Workload
-----------------------------------------

You can set the access type when creating a workload on the CCE console. An Nginx workload is used as an example.

#. In the **Set Application Access** step of :ref:`Creating a Deployment <cce_01_0047>`, :ref:`Creating a StatefulSet <cce_01_0048>`, or :ref:`Creating a DaemonSet <cce_01_0216>`, click **Add Service** and set the following parameters:

   -  **Access Type**: Select **NodePort**.

      .. note::

         If you want to use an EIP to access a NodePort Service through public networks, bind an EIP to the node in the cluster in advance.

   -  **Service Name**: Specify a Service name, which can be the same as the workload name.

   -  .. _cce_01_0142__li195904442002:

      **Service Affinity**: For details, see :ref:`externalTrafficPolicy (Service Affinity) <cce_01_0142__section18134208069>`.

      -  Cluster level: The IP addresses and access ports of all nodes in a cluster can access the workload associated with the Service. Service access will cause performance loss due to route redirection, and the source IP address of the client cannot be obtained.
      -  Node level: Only the IP address and access port of the node where the workload is located can access the workload associated with the Service. Service access will not cause performance loss due to route redirection, and the source IP address of the client can be obtained.

   -  **Port Settings**

      -  **Protocol**: protocol used by the Service.
      -  **Container Port**: port on which the workload in the container image listens. The value ranges from 1 to 65535.
      -  **Access Port**: node port (with a private IP address) to which the container port will be mapped. You are advised to select **Automatically generated**.

         -  **Automatically generated**: The system automatically assigns a port number.
         -  **Specified port**: You have to manually specify a fixed node port number in the range of 30000-32767. Ensure that the port is unique in a cluster.

#. After the configuration is complete, click **OK**.
#. Click **Next: Configure Advanced Settings**. On the page displayed, click **Create**.
#. Click **View Deployment Details** or **View StatefulSet Details**. On the **Services** tab page, obtain the access address, for example, 192.168.0.160:30358.

Adding a Service After Creating a Workload
------------------------------------------

You can set the Service after creating a workload. This has no impact on the workload status and takes effect immediately. The procedure is as follows:

#. Log in to the CCE console. In the navigation pane, choose **Workloads** > **Deployments**. On the workload list, click the name of the workload for which you will create a Service.

   .. note::

      If the Service is associated with an ingress, the ingress is unavailable after the port information of the Service is updated. In this case, you need to delete and recreate the Service.

#. On the **Services** tab page, click **Add Service**.
#. On the **Create Service** page, select **NodePort** from the **Access Type** drop-down list.

   .. note::

      If you want to use an EIP to access a NodePort Service through public networks, bind an EIP to the node in the cluster in advance.

#. Set node access parameters.

   -  **Service Name**: Service name, which can be the same as the workload name.
   -  **Cluster Name**: name of the cluster where the workload runs. The value is inherited from the workload creation page and cannot be changed.
   -  **Namespace**: namespace where the workload is located. The value is inherited from the workload creation page and cannot be changed.
   -  **Workload**: workload for which you want to add a Service. The value is inherited from the workload creation page and cannot be changed.
   -  **Service Affinity**

      -  Cluster level: The IP addresses and access ports of all nodes in a cluster can access the workload associated with the Service. Service access will cause performance loss due to route redirection, and the source IP address of the client cannot be obtained.
      -  Node level: Only the IP address and access port of the node where the workload is located can access the workload associated with the Service. Service access will not cause performance loss due to route redirection, and the source IP address of the client can be obtained.

   -  **Port Settings**

      -  **Protocol**: protocol used by the Service.
      -  **Container Port**: port on which the workload in the container image listens. The Nginx workload listens on port 80.
      -  **Access Port**: node port (with a private IP address) to which the container port will be mapped. You are advised to select **Automatically generated**.

         -  **Automatically generated**: The system automatically assigns a port number.
         -  **Specified port**: You have to manually specify a fixed node port number in the range of 30000-32767. Ensure that the port is unique in a cluster.

#. Click **Create**. A NodePort Service will be added for the workload.

.. _cce_01_0142__section7114174773118:

Using kubectl
-------------

You can run kubectl commands to set the access type. This section uses a Nginx workload as an example to describe how to set a NodePort Service using kubectl.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_01_0107>`.

#. Create and edit the **nginx-deployment.yaml** and **nginx-nodeport-svc.yaml** files.

   The file names are user-defined. **nginx-deployment.yaml** and **nginx-nodeport-svc.yaml** are merely example file names.

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

   **vi nginx-nodeport-svc.yaml**

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
          port: 8080          # Port for accessing a Service.
          protocol: TCP       # Protocol used for accessing a Service. The value can be TCP or UDP.
          targetPort: 80      # Port used by a Service to access the target container. This port is closely related to the applications running in a container. In this example, the Nginx image uses port 80 by default.
        selector:             # Label selector. A Service selects a pod based on the label and forwards the requests for accessing the Service to the pod. In this example, select the pod with the app:nginx label.
          app: nginx
        type: NodePort        # Service type. NodePort indicates that the Service is accessed through a node port.

#. Create a workload.

   **kubectl create -f nginx-deployment.yaml**

   If information similar to the following is displayed, the workload has been created.

   .. code-block::

      deployment "nginx" created

   **kubectl get po**

   If information similar to the following is displayed, the workload is running.

   .. code-block::

      NAME                     READY     STATUS             RESTARTS   AGE
      nginx-2601814895-qhxqv   1/1       Running            0          9s

#. Create a Service.

   **kubectl create -f nginx-nodeport-svc.yaml**

   If information similar to the following is displayed, the Service is being created.

   .. code-block::

      service "nginx-nodeport" created

   **kubectl get svc**

   If information similar to the following is displayed, the Service has been created.

   .. code-block::

      # kubectl get svc
      NAME             TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
      kubernetes       ClusterIP   10.247.0.1     <none>        443/TCP          4d8h
      nginx-nodeport   NodePort    10.247.30.40   <none>        8080:30000/TCP   18s

#. Access the Service.

   By default, a NodePort Service can be accessed by using *Any node IP address:Node port*.

   The Service can be accessed from a node in another cluster in the same VPC or in another pod in the cluster. If a public IP address is bound to the node, you can also use the public IP address to access the Service. Create a container in the cluster and access the container by using *Node IP address:Node port*.

   .. code-block::

      # kubectl get node -owide
      NAME           STATUS   ROLES    AGE    INTERNAL-IP    EXTERNAL-IP   OS-IMAGE                KERNEL-VERSION                CONTAINER-RUNTIME
      10.100.0.136   Ready    <none>   152m   10.100.0.136   <none>        CentOS Linux 7 (Core)   3.10.0-1160.25.1.el7.x86_64   docker://18.9.0
      10.100.0.5     Ready    <none>   152m   10.100.0.5     <none>        CentOS Linux 7 (Core)   3.10.0-1160.25.1.el7.x86_64   docker://18.9.0
      # kubectl run -i --tty --image nginx:alpine test --rm /bin/sh
      If you don't see a command prompt, try pressing enter.
      / # curl 10.100.0.136:30000
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
      / #

.. _cce_01_0142__section18134208069:

externalTrafficPolicy (Service Affinity)
----------------------------------------

For a NodePort Service, requests are first sent to the node port, then the Service, and finally the pod backing the Service. The backing pod may be not located in the node receiving the requests. By default, the backend workload can be accessed from any node IP address and service port. If the pod is not on the node that receives the request, the request will be redirected to the node where the pod is located, which may cause performance loss.

**externalTrafficPolicy** is a configuration parameter of the Service.

.. code-block::

   apiVersion: v1
   kind: Service
   metadata:
     name: nginx-nodeport
   spec:
     externalTrafficPolicy: local
     ports:
     - name: service
       nodePort: 30000
       port: 80
       protocol: TCP
       targetPort: 80
     selector:
       app: nginx
     type: NodePort

If the value of **externalTrafficPolicy** is **local**, requests sent from *Node IP address:Service port* will be forwarded only to the pod on the local node. If the node does not have a pod, the requests are suspended.

The other value of **externalTrafficPolicy** is **cluster** (default value), which indicates that requests are forwarded in a cluster.

You can set this parameter when creating a Service of the NodePort type on the CCE console.

The values of **externalTrafficPolicy** are as follows:

-  **cluster**: The IP addresses and access ports of all nodes in a cluster can access the workload associated with the Service. Service access will cause performance loss due to route redirection, and the source IP address of the client cannot be obtained.
-  **local**: Only the IP address and access port of the node where the workload is located can access the workload associated with the Service. Service access will not cause performance loss due to route redirection, and the source IP address of the client can be obtained.
