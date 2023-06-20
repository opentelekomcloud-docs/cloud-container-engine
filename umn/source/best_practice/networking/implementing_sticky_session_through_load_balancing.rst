:original_name: cce_bestpractice_00231.html

.. _cce_bestpractice_00231:

Implementing Sticky Session Through Load Balancing
==================================================

Concepts
--------

Session persistence is one of the most common while complex problems in load balancing.

Session persistence is also called sticky sessions. After the sticky session function is enabled, requests from the same client are distributed to the same backend ECS by the load balancer for better continuity.

In load balancing and sticky session, connection and session are two key concepts. When only load balancing is concerned, session and connection refer to the same thing.

Simply put, if a user needs to log in, it can be regarded as a session; otherwise, a connection.

The sticky session mechanism fundamentally conflicts with the basic functions of load balancing. A load balancer forwards requests from clients to multiple backend servers to avoid overload on a single server. However, sticky session requires that some requests be forwarded to the same server for processing. Therefore, select a proper sticky session mechanism based on the application environment.

Layer-4 Load Balancing (Service)
--------------------------------

In layer-4 load balancing, source IP address-based sticky session (Hash routing based on the client IP address) can be enabled. To enable source IP address-based sticky session on Services, the following conditions must be met:

**CCE cluster**

#. **Service Affinity** of the Service is set to **Node level** (that is, the value of the **externalTrafficPolicy** field of the Service is **Local**).

   .. note::

      You do not need to set this parameter for CCE Turbo clusters.

#. Enable the source IP address-based sticky session in the load balancing configuration of the Service.

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        name: svc-example
        namespace: default
        annotations:
          kubernetes.io/elb.class: union
          kubernetes.io/elb.id: 56dcc1b4-8810-480c-940a-a44f7736f0dc
          kubernetes.io/elb.lb-algorithm: ROUND_ROBIN
          kubernetes.io/elb.session-affinity-mode: SOURCE_IP
      spec:
        selector:
          app: nginx
        externalTrafficPolicy: Local   # You do not need to set this parameter for CCE Turbo clusters.
        ports:
          - name: cce-service-0
            targetPort: 80
            nodePort: 32633
            port: 80
            protocol: TCP
        type: LoadBalancer

#. Anti-affinity is enabled for the backend application corresponding to the Service.

Layer-7 Load Balancing (Ingress)
--------------------------------

In layer-7 load balancing, sticky session based on HTTP cookies and app cookies can be enabled. To enable such sticky session, the following conditions must be met:

#. The application (workload) corresponding to the ingress is enabled with workload anti-affinity.
#. Node affinity is enabled for the Service corresponding to the ingress.

**Procedure**

#. Create a Nginx workload.

   Set the number of pods to 3 and set the podAntiAffinity.

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
              podAntiAffinity:                   # Pod anti-affinity.
                requiredDuringSchedulingIgnoredDuringExecution:
                  - labelSelector:
                      matchExpressions:
                        - key: app
                          operator: In
                          values:
                            - nginx
                    topologyKey: kubernetes.io/hostname

#. Creating a NodePort Service

   Configure the sticky session in a Service. An ingress can connect to multiple Services, and each Service can have different sticky sessions.

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        name: nginx
        namespace: default
        annotations:
          kubernetes.io/elb.lb-algorithm: ROUND_ROBIN
          kubernetes.io/elb.session-affinity-mode: HTTP_COOKIE      # HTTP cookie type.
          kubernetes.io/elb.session-affinity-option: '{"persistence_timeout":"1440"}'   # Session stickiness duration, in minutes. The value ranges from 1 to 1440.
      spec:
        selector:
          app: nginx
        ports:
          - name: cce-service-0
            protocol: TCP
            port: 80
            targetPort: 80
            nodePort: 32633            # Node port number.
        type: NodePort
        externalTrafficPolicy: Local   # Node-level forwarding.

   You can also select **APP_COOKIE**.

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        name: nginx
        namespace: default
        annotations:
          kubernetes.io/elb.lb-algorithm: ROUND_ROBIN
          kubernetes.io/elb.session-affinity-mode: APP_COOKIE     # Select APP_COOKIE.
          kubernetes.io/elb.session-affinity-option: '{"app_cookie_name":"test"}'  # Application cookie name.
      ...

#. Create an ingress and associate it with a Service. The following example describes how to automatically create a shared load balancer. For details about how to specify other types of load balancers, see `Using kubectl to Create an ELB Ingress <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_10_0252.html>`__.

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        name: ingress-test
        namespace: default
        annotations:
          kubernetes.io/elb.class: union
          kubernetes.io/elb.port: '80'
          kubernetes.io/elb.autocreate:
            '{
                "type":"public",
                "bandwidth_name":"cce-bandwidth-test",
                "bandwidth_chargemode":"traffic",
                "bandwidth_size":1,
                "bandwidth_sharetype":"PER",
                "eip_type":"5_bgp"
              }'
      spec:
        rules:
        - host: 'www.example.com'
          http:
            paths:
            - path: '/'
              backend:
                service:
                  name: nginx     #Service name
                  port:
                    number: 80
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
              pathType: ImplementationSpecific
        ingressClassName: cce

#. Log in to the ELB console, access the load balancer details page, and check whether the sticky session feature is enabled.
