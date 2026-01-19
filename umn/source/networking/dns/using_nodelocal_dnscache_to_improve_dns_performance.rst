:original_name: cce_10_0362.html

.. _cce_10_0362:

Using NodeLocal DNSCache to Improve DNS Performance
===================================================

Challenges
----------

When the number of DNS requests in a cluster increases, the load of CoreDNS increases and the following issues may occur:

-  Increased delay: CoreDNS needs to process more requests, which may slow down the DNS query and affect service performance.
-  Increased resource usage: To ensure DNS performance, CoreDNS requires higher specifications.

Solution
--------

To minimize the impact of DNS delay, deploy NodeLocal DNSCache in the cluster to improve the networking stability and performance. NodeLocal DNSCache runs a DNS cache proxy on cluster nodes. All pods with DNS configurations use the DNS cache proxy running on nodes instead of the CoreDNS service for domain name resolution. This reduces CoreDNS's load and improves the cluster DNS performance.

After NodeLocal DNSCache is enabled, a DNS query goes through the path as shown below.


.. figure:: /_static/images/en-us_image_0000002483959282.png
   :alt: **Figure 1** NodeLocal DNSCache query path

   **Figure 1** NodeLocal DNSCache query path

The resolution rules are as follows:

-  1. By default, the pods with DNSConfig injected use NodeLocal DNSCache to resolve requested domain names.
-  2. If NodeLocal DNSCache cannot resolve domain names, it will ask CoreDNS for resolution.
-  3. CoreDNS uses the DNS server in the VPC to resolve the domain names out of the cluster.
-  4. If a pod with DNSConfig injected cannot access NodeLocal DNSCache, CoreDNS will resolve the domain name.
-  5. By default, CoreDNS resolves domain names for the pods without DNSConfig injected.

Notes and Constraints
---------------------

-  Only clusters of version 1.19 or later support the :ref:`NodeLocal DNSCache <cce_10_0404>` add-on.
-  The **node-local-dns-injection** label is the system label used by NodeLocal DNSCache. Use this label only to :ref:`prevent an automatic DNSConfig injection <cce_10_0362__section1754716106492>`.

.. _cce_10_0362__section0938141194810:

Installing the Add-on
---------------------

CCE provides add-on :ref:`NodeLocal DNSCache <cce_10_0404>` that simplifies the installation of NodeLocal DNSCache.

.. note::

   NodeLocal DNSCache serves as a transparent caching proxy for CoreDNS and does not provide plug-ins such as hosts or rewrite. If you want to enable these plug-ins, modify the CoreDNS configurations.

#. (Optional) Modify the CoreDNS configuration so that the CoreDNS preferentially uses UDP to communicate with the upstream DNS server.

   The NodeLocal DNSCache uses TCP to communicate with the CoreDNS. The CoreDNS communicates with the upstream DNS server based on the protocol used by the request source. However, the cloud server does not support TCP. To use NodeLocal DNSCache, modify the CoreDNS configuration so that UDP is preferentially used to communicate with the upstream DNS server, preventing resolution exceptions.

   To configure CoreDNS to prefer UDP for upstream communication in the forward add-on, you need to add the **prefer_udp** option in the CoreDNS configuration file. To do so, perform the following operations:

   a. Log in to the CCE console and click the cluster name to access the cluster console.

   b. In the navigation pane, choose **Add-ons**. Then, click **Edit** under **CoreDNS**.

   c. Edit the advanced configuration under **Parameters** and modify the following content in the **plugins** field:

      .. code-block::

         {
             "configBlock": "prefer_udp",
             "name": "forward",
             "parameters": ". /etc/resolv.conf"
         }

      Corefile:

      .. code-block::

         Corefile: |-
           .:5353 {
               bind {$POD_IP}
               cache 30 {
                   servfail 5s
               }
               errors
               health {$POD_IP}:8080
               kubernetes cluster.local in-addr.arpa ip6.arpa {
                   pods insecure
                   fallthrough in-addr.arpa ip6.arpa
               }
               loadbalance round_robin
               prometheus {$POD_IP}:9153
               forward . /etc/resolv.conf {
                   prefer_udp
               }
               reload
               ready {$POD_IP}:8081
           }

#. Choose **Add-ons** in the navigation pane, find the **NodeLocal DNSCache** add-on on the right, and click **Install**.

#. On the **Install Add-on** page, select the add-on specifications and set related parameters.

   -  **DNSConfig**: After this function is enabled, a DNSConfig admission controller will be created. The controller intercepts pod creation requests in the namespace labeled with **node-local-dns-injection=enabled** based on admission webhooks and automatically configures DNSConfig for pods. If this function is disabled or the pod belongs to a non-target namespace, you must manually configure DNSConfig for the pod.

      After automatic injection is enabled, you can customize the following configuration items for DNSConfig (supported when the add-on version is 1.6.7 or later):

      .. note::

         If DNSConfig has been configured in the pod when automatic injection is enabled, DNSConfig in the pod will be used first.

      -  (Optional) **IP Address of DNS Server**: IP address list of the DNS server obtained when the container resolves the domain name. NodeLocal DNSCache and CoreDNS IP addresses are added by default. You have the option to add an additional IP address, but duplicates will be removed.
      -  (Optional) **Search Domain**: a search list for host-name lookup. When a domain name cannot be resolved, DNS queries will be attempted combining the domain name with each domain in the search list in turn until a match is found or all domains in the search list are tried. You can add up to three extra search domains, but any duplicates will be removed.
      -  (Optional) **ndots**: specifies that if a domain name has fewer periods (.) than the specified value of **ndots**, it will be combined with the search domain list for DNS query. If the domain name still cannot be resolved, it will be used for DNS query. The system will perform a DNS query on a domain name if the number of periods (.) in it is greater than or equal to the value of **ndots**. If the domain name cannot be resolved, the system will sequentially combine it with the search domain list and then perform a DNS query.

   -  **Target Namespace**: This parameter is available after **DNSConfig** is enabled. Only NodeLocal DNSCache of v1.3.0 or later supports this function.

      -  **Enable All**: CCE adds the **node-local-dns-injection=enabled** label to all created namespaces excluding built-in ones and the ones preset by some add-ons (such as **kube-system**, **kube-public**, **kube-node-lease**, **istio-system**, and **monitoring**), identifies namespace creation requests, and automatically adds the label to newly created namespaces.

         In add-ons of v1.6.77 or later, the **node-local-dns-injection=enabled** label cannot be manually deleted. You can set **node-local-dns-injection** to **disabled** to disable automatic injection in a namespace.

      -  **Manual Configure**: You must manually add the **node-local-dns-injection=enabled** label to the namespaces requiring the injection of DNSConfig. For details, see :ref:`Managing Namespace Labels <cce_10_0285__section124953014352>`.

#. Click **Install**.

.. _cce_10_0362__section454110448388:

Using NodeLocal DNSCache
------------------------

By default, application requests are sent through the CoreDNS proxy. To use node-local-dns as the DNS cache proxy, use any of the following methods:

-  Auto injection: When you create a pod, CCE will automatically configure its **dnsConfig** field. (This function is not available for pods in system namespaces such as kube-system.)
-  Manual configuration: You need to manually configure pod's **dnsConfig** field.

Auto Injection of DNSConfig
---------------------------

To enable auto injection, perform the following operations:

#. Install NodeLocal DNSCache. During the installation, enable **DNSConfig** and configure **Target Namespace** for auto injection. For details, see :ref:`Installing the Add-on <cce_10_0362__section0938141194810>`.

   **Target Namespace**: This parameter is available after **DNSConfig** is enabled. Only NodeLocal DNSCache of v1.3.0 or later supports this function.

   -  **Enable All**: CCE adds the **node-local-dns-injection=enabled** label to all created namespaces excluding built-in ones (such as **kube-system**), identifies namespace creation requests, and automatically adds the label to newly created namespaces.
   -  **Manually Configure**: You must manually add the **node-local-dns-injection=enabled** label to the namespaces requiring the injection of DNSConfig. For details, see :ref:`Managing Namespace Labels <cce_10_0285__section124953014352>`.

#. Create a workload in the namespace with auto injection enabled. For details, see :ref:`Creating a Deployment <cce_10_0047>`.

   .. important::

      The pods for which auto injection is enabled must meet the following requirements:

      -  The new pod does not run in system namespaces such as kube-system and kube-public.
      -  The **node-local-dns-injection=disabled** label for disabling DNS injection is not added to the new pod.
      -  The new pod's **DNSPolicy** is **ClusterFirstWithHostNet**. Alternatively, the pod does not use the host network and **DNSPolicy** is **ClusterFirst**.

#. Run the following command to check the DNS configuration of the target pod:

   .. code-block::

      kubectl get pod <pod_name> -oyaml

   After auto injection is enabled, the following **dnsConfig** settings will be automatically added to the created pod. In addition to the NodeLocal DNSCache IP address 169.254.20.10, the CoreDNS IP address 10.247.3.10 is added to **nameservers**, ensuring high availability of the service DNS server.

   .. code-block::

      ...
        dnsConfig:
          nameservers:
            - 169.254.20.10
            - 10.247.3.10
          searches:
            - default.svc.cluster.local
            - svc.cluster.local
            - cluster.local
          options:
            - name: timeout
              value: ''
            - name: ndots
              value: '5'
            - name: single-request-reopen
      ...

Manually Configuring DNSConfig
------------------------------

Manually add the **dnsConfig** settings to pods.

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Create a pod and add the NodeLocal DNSCache IP address 169.254.20.10 to **nameservers** in **dnsConfig**.

   Create a **nginx.yaml** file. The following shows an example:

   .. code-block::

      apiVersion: v1
      kind: Pod
      metadata:
        name: nginx
      spec:
        containers:
        - image: nginx:alpine
          name: container-0
        dnsConfig:
          nameservers:
          - 169.254.20.10
          - 10.247.3.10
          searches:
          - default.svc.cluster.local
          - svc.cluster.local
          - cluster.local
          options:
          - name: ndots
            value: '2'
        imagePullSecrets:
        - name: default-secret

#. Run the following command to create a pod:

   .. code-block::

      kubectl create -f nginx.yaml

.. _cce_10_0362__section1754716106492:

Common Issues
-------------

-  How Do I Prevent Auto DNSConfig Injection on a Specific Pod?

   **Solution**:

   Add **node-local-dns-injection: disabled** to the **labels** field in the pod template. Example:

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
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
              node-local-dns-injection: disabled   # Prevent auto DNSConfig injection.
          spec:
            containers:
              - name: container-1
                image: nginx:latest
                imagePullPolicy: IfNotPresent
            imagePullSecrets:
              - name: default-secret
