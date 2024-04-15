:original_name: cce_10_0365.html

.. _cce_10_0365:

DNS Configuration
=================

Every Kubernetes cluster has a built-in DNS add-on (Kube-DNS or CoreDNS) to provide domain name resolution for workloads in the cluster. When handling a high concurrency of DNS queries, Kube-DNS/CoreDNS may encounter a performance bottleneck, that is, it may fail occasionally to fulfill DNS queries. There are cases when Kubernetes workloads initiate unnecessary DNS queries. This makes DNS overloaded if there are many concurrent DNS queries. Tuning DNS configuration for workloads will reduce the risks of DNS query failures to some extent.

For more information about DNS, see :ref:`CoreDNS <cce_10_0129>`.

DNS Configuration Items
-----------------------

Run the **cat /etc/resolv.conf** command on a Linux node or container to view the DNS resolver configuration file. The following is an example DNS resolver configuration of a container in a Kubernetes cluster:

.. code-block::

   nameserver 10.247.x.x
   search default.svc.cluster.local svc.cluster.local cluster.local
   options ndots:5

**Configuration Options**

-  **nameserver**: an IP address list of a name server that the resolver will query. If this parameter is set to 10.247.x.x, the resolver will query the kube-dns/CoreDNS. If this parameter is set to another IP address, the resolver will query a cloud or on-premises DNS server.

-  **search**: a search list for host-name lookup. When a domain name cannot be resolved, DNS queries will be attempted combining the domain name with each domain in the search list in turn until a match is found or all domains in the search list are tried. For CCE clusters, the search list is currently limited to three domains per container. When a nonexistent domain name is being resolved, eight DNS queries will be initiated because each domain name (including those in the search list) will be queried twice, one for IPv4 and the other for IPv6.

-  **options**: options that allow certain internal resolver variables to be modified. Common options include timeout and ndots.

   The value **ndots:5** means that if a domain name has fewer than 5 dots (.), DNS queries will be attempted by combining the domain name with each domain in the search list in turn. If no match is found after all the domains in the search list are tried, the domain name is then used for DNS query. If the domain name has 5 or more than 5 dots, it will be tried first for DNS query. In case that the domain name cannot be resolved, DNS queries will be attempted by combining the domain name with each domain in the search list in turn.

   For example, the domain name **www.***.com** has only two dots (smaller than the value of **ndots**), and therefore the sequence of DNS queries is as follows: **www.***.default.svc.cluster.local**, **www.***.com.svc.cluster.local**, **www.***.com.cluster.local**, and **www.***.com**. This means that at least seven DNS queries will be initiated before the domain name is resolved into an IP address. It is clear that when many unnecessary DNS queries will be initiated to access an external domain name. There is room for improvement in workload's DNS configuration.

.. note::

   For more information about configuration options in the resolver configuration file used by Linux operating systems, visit http://man7.org/linux/man-pages/man5/resolv.conf.5.html.

Configuring DNS for a Workload Using the Console
------------------------------------------------

Kubernetes provides DNS-related configuration options for applications. The use of application's DNS configuration can effectively reduce unnecessary DNS queries in certain scenarios and improve service concurrency. The following procedure uses an Nginx application as an example to describe how to add DNS configurations for a workload on the console.

#. Log in to the CCE console, access the cluster console, select **Workloads** in the navigation pane, and click **Create Workload** in the upper right corner.
#. Configure basic information about the workload. For details, see :ref:`Creating a Workload <cce_10_0673>`.
#. In the **Advanced Settings** area, click the **DNS** tab and set the following parameters as required:

   -  **DNS Policy**: The DNS policies provided on the console correspond to the **dnsPolicy** field in the YAML file. For details, see :ref:`Table 1 <cce_10_0365__table144443315261>`.

      -  **Supplement defaults**: corresponds to **dnsPolicy=ClusterFirst**. Containers can resolve both the cluster-internal domain names registered by a Service and the external domain names exposed to public networks.
      -  **Replace defaults**: corresponds to **dnsPolicy=None**. You must configure **IP Address** and **Search Domain**. Containers only use the user-defined IP address and search domain configurations for domain name resolution.
      -  **Inherit defaults**: corresponds to **dnsPolicy=Default**. Containers use the domain name resolution configuration from the node that pods run on and cannot resolve the cluster-internal domain names.

   -  **Optional Objects**: The options parameters in the :ref:`dnsConfig field <cce_10_0365__table16581121652515>`. Each object may have a name property (required) and a value property (optional). After setting the properties, click **confirm to add**.

      -  **timeout**: Timeout interval, in seconds.
      -  **ndots**: Number of dots (.) that must be present in a domain name. If a domain name has dots fewer than this value, the operating system will look up the name in the search domain. If not, the name is a fully qualified domain name (FQDN) and will be tried first as an absolute name.

   -  **IP Address**: **nameservers** in the :ref:`dnsConfig <cce_10_0365__table16581121652515>`. You can configure the domain name server for the custom domain name. The value is one or a group of DNS IP addresses.
   -  **Search Domain**: **searches** in the :ref:`dnsConfig <cce_10_0365__table16581121652515>`. A list of DNS search domains for hostname lookup in the pod. This property is optional. When specified, the provided list will be merged into the search domain names generated from the chosen DNS policy in **dnsPolicy**. Duplicate domain names are removed.

#. Click **Create Workload**.

Configuring DNS Using the Workload YAML
---------------------------------------

When creating a workload using a YAML file, you can configure the DNS settings in the YAML. The following is an example for an Nginx application:

.. code-block::

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: nginx
     namespace: default
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
           - name: container-1
             image: nginx:latest
             imagePullPolicy: IfNotPresent
         imagePullSecrets:
           - name: default-secret
         dnsPolicy: None
         dnsConfig:
           options:
             - name: ndots
               value: '5'
             - name: timeout
               value: '3'
           nameservers:
             - 10.2.3.4
           searches:
             - my.dns.search.suffix

-  **dnsPolicy**

   The **dnsPolicy** field is used to configure a DNS policy for an application. The default value is **ClusterFirst**. The following table lists **dnsPolicy** configurations.

   .. _cce_10_0365__table144443315261:

   .. table:: **Table 1** dnsPolicy

      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
      +===================================+=====================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================================+
      | ClusterFirst (default value)      | Custom DNS configuration added to the default DNS configuration. By default, the application connects to CoreDNS (CoreDNS of the CCE cluster connects to the DNS on the cloud by default). The custom dnsConfig will be added to the default DNS parameters. Containers can resolve both the cluster-internal domain names registered by a Service and the external domain names exposed to public networks. The search list (**search** option) and **ndots: 5** are present in the DNS configuration file. Therefore, when accessing an external domain name and a long cluster-internal domain name (for example, kubernetes.default.svc.cluster.local), the search list will usually be traversed first, resulting in at least six invalid DNS queries. The issue of invalid DNS queries disappears only when a short cluster-internal domain name (for example, kubernetes) is being accessed. |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | ClusterFirstWithHostNet           | By default, the applications configured with the :ref:`host network <cce_10_0402>` are interconnected with the DNS configuration of the node where the pod is located. The DNS configuration is specified in the DNS file that the kubelet **--resolv-conf** parameter points to. In this case, the CCE cluster uses the DNS on the cloud. If workloads need to use Kube-DNS/CoreDNS of the cluster, set **dnsPolicy** to **ClusterFirstWithHostNet** and container's DNS configuration file is the same as ClusterFirst, in which invalid DNS queries still exist.                                                                                                                                                                                                                                                                                                                                 |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                   | .. code-block::                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                   |    ...                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
      |                                   |    spec:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
      |                                   |      containers:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
      |                                   |      - image: nginx:latest                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
      |                                   |        imagePullPolicy: IfNotPresent                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
      |                                   |        name: container-1                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
      |                                   |      restartPolicy: Always                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
      |                                   |      hostNetwork: true                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
      |                                   |      dnsPolicy: ClusterFirstWithHostNet                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Default                           | The DNS configuration of the node where the pod is located is inherited, and the custom DNS configuration is added to the inherited configuration. Container's DNS configuration file is the DNS configuration file that the kubelet's **--resolv-conf** flag points to. In this case, a cloud DNS is used for CCE clusters. Both **search** and **options** fields are left unspecified. This configuration can only resolve the external domain names registered with the Internet, and not cluster-internal domain names. This configuration is free from the issue of invalid DNS queries.                                                                                                                                                                                                                                                                                                      |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | None                              | The default DNS configuration is replaced by the custom DNS configuration, and only the custom DNS configuration is used. If **dnsPolicy** is set to **None**, the **dnsConfig** field must be specified because all DNS settings are supposed to be provided using the **dnsConfig** field.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. note::

      If the **dnsPolicy** field is not specified, the default value is **ClusterFirst** instead of **Default**.

-  **dnsConfig**

   The **dnsConfig** field is used to configure DNS parameters for workloads. The configured parameters are merged to the DNS configuration file generated according to **dnsPolicy**. If **dnsPolicy** is set to **None**, the workload's DNS configuration file is specified by the **dnsConfig** field. If **dnsPolicy** is not set to **None**, the DNS parameters configured in **dnsConfig** are added to the DNS configuration file generated according to **dnsPolicy**.

   .. _cce_10_0365__table16581121652515:

   .. table:: **Table 2** dnsConfig

      +-------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter   | Description                                                                                                                                                                                                                                                                                                                                    |
      +=============+================================================================================================================================================================================================================================================================================================================================================+
      | options     | An optional list of objects where each object may have a name property (required) and a value property (optional). The contents in this property will be merged to the options generated from the specified DNS policy in **dnsPolicy**. Duplicate entries are removed.                                                                        |
      +-------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | nameservers | A list of IP addresses that will be used as DNS servers. If workload's **dnsPolicy** is set to **None**, the list must contain at least one IP address, otherwise this property is optional. The servers listed will be combined to the nameservers generated from the specified DNS policy in **dnsPolicy** with duplicate addresses removed. |
      +-------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | searches    | A list of DNS search domains for hostname lookup in the pod. This property is optional. When specified, the provided list will be merged into the search domain names generated from the chosen DNS policy in **dnsPolicy**. Duplicate domain names are removed. Kubernetes allows for at most 6 search domains.                               |
      +-------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Configuration Examples
----------------------

The following example describes how to configure DNS for workloads.

-  **Use Case 1: Using Kube-DNS/CoreDNS Built in Kubernetes Clusters**

   **Scenario**

   Kubernetes in-cluster Kube-DNS/CoreDNS applies to resolving only cluster-internal domain names or cluster-internal domain names + external domain names. This is the default DNS for workloads.

   **Example:**

   .. code-block::

      apiVersion: v1
      kind: Pod
      metadata:
        namespace: default
        name: dns-example
      spec:
        containers:
        - name: test
          image: nginx:alpine
        dnsPolicy: ClusterFirst
        imagePullSecrets:
          - name: default-secret

   Container's DNS configuration file:

   .. code-block::

      nameserver 10.247.3.10
      search default.svc.cluster.local svc.cluster.local cluster.local
      options ndots:5

-  **Use Case 2: Using a Cloud DNS**

   **Scenario**

   A DNS cannot resolve cluster-internal domain names and therefore applies to the scenario where workloads access only external domain names registered with the Internet.

   **Example:**

   .. code-block::

      apiVersion: v1
      kind: Pod
      metadata:
        namespace: default
        name: dns-example
      spec:
        containers:
        - name: test
          image: nginx:alpine
        dnsPolicy: Default  # The DNS configuration file that the kubelet --resolv-conf parameter points to is used. In this case, the CCE cluster uses the DNS on the cloud.
        imagePullSecrets:
          - name: default-secret

   Container's DNS configuration file:

   .. code-block::

      nameserver 100.125.x.x

-  **Use Case 3: Using Kube-DNS/CoreDNS for Workloads Running with hostNetwork**

   **Scenario**

   By default, a DNS is used for workloads running with hostNetwork. If workloads need to use Kube-DNS/CoreDNS, set **dnsPolicy** to **ClusterFirstWithHostNet**.

   **Example:**

   .. code-block::

      apiVersion: v1
      kind: Pod
      metadata:
        name: nginx
      spec:
        hostNetwork: true
        dnsPolicy: ClusterFirstWithHostNet
        containers:
        - name: nginx
          image: nginx:alpine
          ports:
          - containerPort: 80
        imagePullSecrets:
          - name: default-secret

   Container's DNS configuration file:

   .. code-block::

      nameserver 10.247.3.10
      search default.svc.cluster.local svc.cluster.local cluster.local
      options ndots:5

-  **Use Case 4: Customizing Application's DNS Configuration**

   **Scenario**

   You can flexibly customize the DNS configuration file for applications. Using **dnsPolicy** and **dnsConfig** together can address almost all scenarios, including the scenarios in which an on-premises DNS will be used, multiple DNSs will be cascaded, and DNS configuration options will be modified.

   **Example 1: Using Your On-Premises DNS**

   *Set* **dnsPolicy** *to* **None** *so application's DNS configuration file is generated based on* **dnsConfig**\ *.*

   .. code-block::

      apiVersion: v1
      kind: Pod
      metadata:
        namespace: default
        name: dns-example
      spec:
        containers:
        - name: test
          image: nginx:alpine
        dnsPolicy: "None"
        dnsConfig:
          nameservers:
          - 10.2.3.4  # IP address of your on-premises DNS
          searches:
          - ns1.svc.cluster.local
          - my.dns.search.suffix
          options:
          - name: ndots
            value: "2"
          - name: timeout
            value: "3"
        imagePullSecrets:
          - name: default-secret

   Container's DNS configuration file:

   .. code-block::

      nameserver 10.2.3.4
      search ns1.svc.cluster.local my.dns.search.suffix
      options timeout:3 ndots:2

   **Example 2: Modifying the ndots Option in the DNS Configuration File to Reduce Invalid DNS Queries**

   Set **dnsPolicy** to a value other than **None** so the DNS parameters configured in **dnsConfig** are added to the DNS configuration file generated based on **dnsPolicy**.

   .. code-block::

      apiVersion: v1
      kind: Pod
      metadata:
        namespace: default
        name: dns-example
      spec:
        containers:
        - name: test
          image: nginx:alpine
        dnsPolicy: "ClusterFirst"
        dnsConfig:
          options:
          - name: ndots
            value: "2" # The ndots:5 option in the DNS configuration file generated based on the ClusterFirst policy is changed to ndots:2.
        imagePullSecrets:
          - name: default-secret

   Container's DNS configuration file:

   .. code-block::

      nameserver 10.247.3.10
      search default.svc.cluster.local svc.cluster.local cluster.local
      options ndots:2

   **Example 3: Using Multiple DNSs in Serial Sequence**

   .. code-block::

      apiVersion: v1
      kind: Pod
      metadata:
        namespace: default
        name: dns-example
      spec:
        containers:
        - name: test
          image: nginx:alpine
        dnsPolicy: ClusterFirst  # Added DNS configuration. The cluster connects to CoreDNS by default.
        dnsConfig:
          nameservers:
          - 10.2.3.4 # IP address of your on-premises DNS
        imagePullSecrets:
          - name: default-secret

   Container's DNS configuration file:

   .. code-block::

      nameserver 10.247.3.10 10.2.3.4
      search default.svc.cluster.local svc.cluster.local cluster.local
      options ndots:5
