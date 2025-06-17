:original_name: cce_bestpractice_00231.html

.. _cce_bestpractice_00231:

Implementing Sticky Session Through Load Balancing
==================================================

Concepts
--------

Sticky sessions ensure continuity and consistency when you access applications. If a load balancer is deployed between a client and backend servers, connections may be forwarded to different servers for processing. Sticky sessions can resolve this issue. After sticky session is enabled, requests from the same client will be continuously distributed to the same backend server through load balancing.

For example, in most online systems that require user identity authentication, a user needs to interact with the server for multiple times to complete a session. These interactions require continuity. If sticky session is not configured, the load balancer may allocate certain requests to different backend servers. Since user identity has not been authenticated on other backend servers, interaction exceptions such as a user login failure may occur.

Therefore, select a proper sticky session type based on the application environment.

.. table:: **Table 1** Sticky session types

   +-----------------+------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------+
   | OSI Layer       | Listener Protocol and Networking   | Sticky Session Type                                                                                                                                                                                                                                                                                                                                                | Scenarios Where Sticky Sessions Become Invalid                                                    |
   +=================+====================================+====================================================================================================================================================================================================================================================================================================================================================================+===================================================================================================+
   | Layer 4         | TCP- or UDP-compliant Services     | **Source IP address**: The source IP address of each request is calculated using the consistent hashing algorithm to obtain a unique hashing key, and all backend servers are numbered. The system allocates the client to a particular server based on the generated key. This allows requests from the same IP address are forwarded to the same backend server. | -  Source IP addresses of the clients have changed.                                               |
   |                 |                                    |                                                                                                                                                                                                                                                                                                                                                                    | -  Requests from the clients exceed the session stickiness duration.                              |
   +-----------------+------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------+
   | Layer 7         | HTTP- or HTTPS-compliant ingresses | -  **Load balancer cookie**: The load balancer generates a cookie after receiving a request from the client. All subsequent requests with the cookie will be routed to the same backend server.                                                                                                                                                                    | -  If requests sent by the clients do not contain a cookie, sticky sessions will not take effect. |
   |                 |                                    | -  **Application cookie**: The application deployed on the backend server generates a cookie after receiving the first request from the client. All subsequent requests with the same cookie will be routed to the same backend server.                                                                                                                            | -  Requests from the clients exceed the session stickiness duration.                              |
   +-----------------+------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------+

.. note::

   When creating a load balancer, configure sticky sessions by setting **kubernetes.io/elb.lb-algorithm** to **ROUND_ROBIN** or **kubernetes.io/elb.lb-algorithm** to **LEAST_CONNECTIONS**. If you set **kubernetes.io/elb.lb-algorithm** is to **SOURCE_IP**, source IP address-based sticky sessions are supported. In this case, you do not need to configure sticky sessions again.

Enabling Layer 4 Sticky Session in a CCE Standard Cluster
---------------------------------------------------------

In a CCE standard cluster, to enable source IP address-based sticky session for a Service, ensure the following conditions are met:

#. **Service Affinity** of the Service must be set to **Node-level**, where the **externalTrafficPolicy** value of the Service must be **Local**.
#. Anti-affinity has been enabled on the backend applications of the Service to prevent all pods from being deployed on the same node.

**Procedure**

#. Create an Nginx workload.

   Set the number of pods to 3 and configure podAntiAffinity.

   .. code-block::

      kind: Deployment
      apiVersion: apps/v1
      metadata:
        name: nginx
        namespace: default
      spec:
        replicas: 3
        selector:
          matchLabels:
            app: nginx
        template:
          metadata:
            labels:
              app: nginx
          spec:
            containers:
              - name: container-0
                image: 'nginx:perl'
                resources:
                  limits:
                    cpu: 250m
                    memory: 512Mi
                  requests:
                    cpu: 250m
                    memory: 512Mi
            imagePullSecrets:
              - name: default-secret
            affinity:
              podAntiAffinity:                   # Pod anti-affinity
                requiredDuringSchedulingIgnoredDuringExecution:
                  - labelSelector:
                      matchExpressions:
                        - key: app
                          operator: In
                          values:
                            - nginx
                    topologyKey: kubernetes.io/hostname

#. Create a LoadBalancer Service, for example, using an existing load balancer. The following shows an example YAML file for configuring source IP address-based sticky sessions:

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        name: svc-example
        namespace: default
        annotations:
          kubernetes.io/elb.class: union
          kubernetes.io/elb.id: *****
          kubernetes.io/elb.lb-algorithm: ROUND_ROBIN      # Weighted round robin allocation policy
          kubernetes.io/elb.session-affinity-mode: SOURCE_IP    # Enable source IP address-based sticky session.
      spec:
        selector:
          app: nginx
        externalTrafficPolicy: Local    # Node level Service affinity
        ports:
          - name: cce-service-0
            targetPort: 80
            nodePort: 32633
            port: 80
            protocol: TCP
        type: LoadBalancer

#. Check whether the Layer 4 sticky session function is enabled.

   a. Log in to the ELB console, locate the row containing the target load balancer, and click the listener name.

   b. Check whether the sticky session function is enabled in the backend server group.

Enabling Layer 4 Sticky Session in a CCE Turbo Cluster
------------------------------------------------------

In a CCE Turbo cluster, enabling source IP address-based sticky session for a Service relies on the load balancer type.

-  When a dedicated load balancer is used, passthrough networking is allowed between the load balancer and pods, and pods function as the backend server group of the load balancer. Therefore, you do not need to configure Service affinity or application anti-affinity when enabling source IP address-based sticky session for the Service.
-  If a shared load balancer is used, sticky session cannot be enabled.

**Procedure**

-  **For dedicated load balancers**

   The following shows an example YAML file for configuring source IP address-based sticky sessions for a Service that uses an existing load balancer:

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        name: svc-example
        namespace: default
        annotations:
          kubernetes.io/elb.class: performance
          kubernetes.io/elb.id: *****
          kubernetes.io/elb.lb-algorithm: ROUND_ROBIN      # Weighted round robin allocation policy
          kubernetes.io/elb.session-affinity-mode: SOURCE_IP    # Enable source IP address-based sticky session.
      spec:
        selector:
          app: nginx
        externalTrafficPolicy: Cluster     # In CCE Turbo clusters, Service affinity does not need to be configured if a dedicated load balancer is used.
        ports:
          - name: cce-service-0
            targetPort: 80
            nodePort: 32633
            port: 80
            protocol: TCP
        type: LoadBalancer

   Verify that the Layer 4 sticky session function is enabled.

   #. Log in to the ELB console, locate the row containing the target load balancer, and click the listener name.

   #. Check whether the sticky session function is enabled in the backend server group.

Enabling Layer 7 Sticky Session in a CCE Standard Cluster
---------------------------------------------------------

To enable cookie-based sticky session on an ingress, ensure the following conditions are met:

#. **Service Affinity** of the ingress must be set to **Node-level**, where the **externalTrafficPolicy** value of the Service must be **Local**.
#. Anti-affinity must be enabled for the ingress workload to prevent all pods from being deployed on the same node.

**Procedure**

#. Create an Nginx workload.

   Set the number of pods to 3 and configure podAntiAffinity.

   .. code-block::

      kind: Deployment
      apiVersion: apps/v1
      metadata:
        name: nginx
        namespace: default
      spec:
        replicas: 3
        selector:
          matchLabels:
            app: nginx
        template:
          metadata:
            labels:
              app: nginx
          spec:
            containers:
              - name: container-0
                image: 'nginx:perl'
                resources:
                  limits:
                    cpu: 250m
                    memory: 512Mi
                  requests:
                    cpu: 250m
                    memory: 512Mi
            imagePullSecrets:
              - name: default-secret
            affinity:
              podAntiAffinity:                   # Pod anti-affinity
                requiredDuringSchedulingIgnoredDuringExecution:
                  - labelSelector:
                      matchExpressions:
                        - key: app
                          operator: In
                          values:
                            - nginx
                    topologyKey: kubernetes.io/hostname

#. Create a Service for the workload. This section uses a NodePort Service as an example.

   Configure sticky sessions during the creation of a Service. An ingress can access multiple Services, and each Service can have different sticky sessions.

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        name: nginx
        namespace: default
        annotations:
          kubernetes.io/elb.lb-algorithm: ROUND_ROBIN      # Weighted round robin allocation policy
          kubernetes.io/elb.session-affinity-mode: HTTP_COOKIE      # HTTP cookie
          kubernetes.io/elb.session-affinity-option: '{"persistence_timeout":"1440"}'   # Session stickiness duration, in minutes. The value ranges from 1 to 1440.
      spec:
        selector:
          app: nginx
        ports:
          - name: cce-service-0
            protocol: TCP
            port: 80
            targetPort: 80
            nodePort: 32633            # Custom node port
        type: NodePort
        externalTrafficPolicy: Local   # Node level Service affinity

   You can also select **APP_COOKIE**.

   .. important::

      Only shared load balancers support application cookie-based sticky sessions.

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        name: nginx
        namespace: default
        annotations:
          kubernetes.io/elb.lb-algorithm: ROUND_ROBIN      # Weighted round robin allocation policy
          kubernetes.io/elb.session-affinity-mode: APP_COOKIE     # Select APP_COOKIE.
          kubernetes.io/elb.session-affinity-option: '{"app_cookie_name":"test"}'  # Application cookie name
      spec:
        selector:
          app: nginx
        ports:
          - name: cce-service-0
            protocol: TCP
            port: 80
            targetPort: 80
            nodePort: 32633            # Custom node port
        type: NodePort
        externalTrafficPolicy: Local   # Node level Service affinity

#. Create an ingress and associate it with the Service. The following uses an existing load balancer as an example. For details about how to automatically create a load balancer, see `Using kubectl to Create a LoadBalancer Ingress <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_10_0252.html>`__.

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        name: ingress-test
        namespace: default
        annotations:
          kubernetes.io/elb.class: union
          kubernetes.io/elb.port: '80'
          kubernetes.io/elb.id: *****
      spec:
        rules:
        - host: 'www.example.com'
          http:
            paths:
            - path: '/'
              backend:
                service:
                  name: nginx     # Service name
                  port:
                    number: 80
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
              pathType: ImplementationSpecific
        ingressClassName: cce

#. Verify that the Layer 7 sticky session function is enabled.

   a. Log in to the ELB console, locate the row containing the target load balancer, and click the listener name.
   b. Click the **Forwarding Policies** tab, click the backend server group name, and check whether sticky session is enabled for it.

Enabling Layer 7 Sticky Session in a CCE Turbo Cluster
------------------------------------------------------

Enable cookie-based sticky session on the ingress.

-  When a dedicated load balancer is used, passthrough networking is allowed between the load balancer and pods, and pods function as the backend server group of the load balancer. Therefore, you do not need to configure Service affinity or application anti-affinity when enabling cookie-based sticky session for the ingress.
-  If a shared load balancer is used, sticky session cannot be enabled.

**Procedure**

-  **For dedicated load balancers**

   #. Create a Service for the workload. In a CCE Turbo cluster, the ingresses that use a dedicated load balancer must interconnect with ClusterIP Services.

      Configure sticky sessions during the creation of a Service. An ingress can access multiple Services, and each Service can have different sticky sessions.

      .. code-block::

         apiVersion: v1
         kind: Service
         metadata:
           name: nginx
           namespace: default
           annotations:
             kubernetes.io/elb.lb-algorithm: ROUND_ROBIN      # Weighted round robin allocation policy
             kubernetes.io/elb.session-affinity-mode: HTTP_COOKIE      # HTTP cookie
             kubernetes.io/elb.session-affinity-option: '{"persistence_timeout":"1440"}'   # Session stickiness duration, in minutes. The value ranges from 1 to 1440.
         spec:
           selector:
             app: nginx
           ports:
             - name: cce-service-0
               protocol: TCP
               port: 80
               targetPort: 80
               nodePort: 0
           type: ClusterIP

   #. Create an ingress and associate it with the Service. The following uses an existing load balancer as an example. For details about how to automatically create a load balancer, see `Using kubectl to Create a LoadBalancer Ingress <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_10_0252.html>`__.

      .. code-block::

         apiVersion: networking.k8s.io/v1
         kind: Ingress
         metadata:
           name: ingress-test
           namespace: default
           annotations:
             kubernetes.io/elb.class: performance
             kubernetes.io/elb.port: '80'
             kubernetes.io/elb.id: *****
         spec:
           rules:
           - host: 'www.example.com'
             http:
               paths:
               - path: '/'
                 backend:
                   service:
                     name: nginx     # Service name
                     port:
                       number: 80
                 property:
                   ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
                 pathType: ImplementationSpecific
           ingressClassName: cce

   #. Verify that the Layer 7 sticky session function is enabled.

      a. Log in to the ELB console, locate the row containing the target load balancer, and click the listener name.
      b. Click the **Forwarding Policies** tab, click the backend server group name, and check whether sticky session is enabled for it.
