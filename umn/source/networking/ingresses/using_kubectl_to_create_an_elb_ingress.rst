:original_name: cce_10_0252.html

.. _cce_10_0252:

Using kubectl to Create an ELB Ingress
======================================

Scenario
--------

This section uses an :ref:`Nginx workload <cce_10_0047__section155246177178>` as an example to describe how to create an ELB ingress using kubectl.

-  If no load balancer is available in the same VPC, CCE can automatically create a load balancer when creating an ingress. For details, see :ref:`Creating an Ingress - Automatically Creating a Load Balancer <cce_10_0252__section3675115714214>`.
-  If a load balancer is available in the same VPC, perform the operation by referring to :ref:`Creating an Ingress - Interconnecting with an Existing Load Balancer <cce_10_0252__section32300431736>`.

Prerequisites
-------------

-  An ingress provides network access for backend workloads. Ensure that a workload is available in a cluster. If no workload is available, deploy a sample Nginx workload by referring to :ref:`Creating a Deployment <cce_10_0047>`, :ref:`Creating a StatefulSet <cce_10_0048>`, or :ref:`Creating a DaemonSet <cce_10_0216>`.
-  A NodePort Service has been configured for the workload. For details about how to configure the Service, see :ref:`NodePort <cce_10_0142>`.
-  Dedicated load balancers must be the application type (HTTP/HTTPS) supporting private networks (with a private IP).

.. _cce_10_0252__section084115985013:

Ingress Description of networking.k8s.io/v1
-------------------------------------------

In CCE clusters of v1.23 or later, the ingress version is switched to networking.k8s.io/v1.

Compared with v1beta1, v1 has the following differences in parameters:

-  The ingress type is changed from **kubernetes.io/ingress.class** in **annotations** to **spec.ingressClassName**.
-  The format of **backend** is changed.
-  The **pathType** parameter must be specified for each path. The options are as follows:

   -  **ImplementationSpecific**: The matching method depends on Ingress Controller. The matching method defined by **ingress.beta.kubernetes.io/url-match-mode** is used in CCE, which is the same as v1beta1.
   -  **Exact**: exact matching of the URL, which is case-sensitive.
   -  **Prefix**: matching based on the URL prefix separated by a slash (/). The match is case-sensitive, and elements in the path are matched one by one. A path element refers to a list of labels in the path separated by a slash (/).

|image1|

.. _cce_10_0252__section3675115714214:

Creating an Ingress - Automatically Creating a Load Balancer
------------------------------------------------------------

The following describes how to run the kubectl command to automatically create a load balancer when creating an ingress.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create a YAML file named **ingress-test.yaml**. The file name can be customized.

   **vi ingress-test.yaml**

   .. note::

      Starting from cluster v1.23, the ingress version is switched from **networking.k8s.io/v1beta1** to **networking.k8s.io/v1**. For details about the differences between v1 and v1beta1, see :ref:`Ingress Description of networking.k8s.io/v1 <cce_10_0252__section084115985013>`.

   **Example of a shared load balancer (public network access) for clusters of v1.23 or later:**

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        name: ingress-test
        annotations:
          kubernetes.io/elb.class: union
          kubernetes.io/elb.port: '80'
          kubernetes.io/elb.autocreate:
            '{
                "type":"public",
                "bandwidth_name":"cce-bandwidth-******",
                "bandwidth_chargemode":"bandwidth",
                "bandwidth_size":5,
                "bandwidth_sharetype":"PER",
                "eip_type":"5_bgp"
              }'
      spec:
        rules:
        - host: ''
          http:
            paths:
            - path: '/'
              backend:
                service:
                  name: <your_service_name>  # Replace it with the name of your target Service.
                  port:
                    number: 8080             # Replace 8080 with the port number of your target Service.
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
              pathType: ImplementationSpecific
        ingressClassName: cce    # ELB ingress is used.

   **Example of a shared load balancer (public network access) for clusters of v1.21 or earlier:**

   .. code-block::

      apiVersion: networking.k8s.io/v1beta1
      kind: Ingress
      metadata:
        name: ingress-test
        annotations:
          kubernetes.io/elb.class: union
          kubernetes.io/ingress.class: cce    # ELB ingress is used.
          kubernetes.io/elb.port: '80'
          kubernetes.io/elb.autocreate:
            '{
                "type":"public",
                "bandwidth_name":"cce-bandwidth-******",
                "bandwidth_chargemode":"bandwidth",
                "bandwidth_size":5,
                "bandwidth_sharetype":"PER",
                "eip_type":"5_bgp"
              }'
      spec:
        rules:
        - host: ''
          http:
            paths:
            - path: '/'
              backend:
                serviceName: <your_service_name>  # Replace it with the name of your target Service.
                servicePort: 80
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH

   **Example of a dedicated load balancer (public network access) for clusters of v1.23 or later:**

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        name: ingress-test
        namespace: default
        annotations:
          kubernetes.io/elb.class: performance
          kubernetes.io/elb.port: '80'
          kubernetes.io/elb.autocreate:
            '{
                "type": "public",
                "bandwidth_name": "cce-bandwidth-******",
                "bandwidth_chargemode": "bandwidth",
                "bandwidth_size": 5,
                "bandwidth_sharetype": "PER",
                "eip_type": "5_bgp",
                "available_zone": [
                    "eu-de-01"
                ],
                "l7_flavor_name": "L7_flavor.elb.s1.small"
             }'
      spec:
        rules:
        - host: ''
          http:
            paths:
            - path: '/'
              backend:
                service:
                  name: <your_service_name>  # Replace it with the name of your target Service.
                  port:
                    number: 8080             # Replace 8080 with the port number of your target Service.
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
              pathType: ImplementationSpecific
        ingressClassName: cce

   **Example of a dedicated load balancer (public network access) for clusters of version 1.21 or earlier:**

   .. code-block::

      apiVersion: networking.k8s.io/v1beta1
      kind: Ingress
      metadata:
        name: ingress-test
        namespace: default
        annotations:
          kubernetes.io/elb.class: performance
          kubernetes.io/ingress.class: cce
          kubernetes.io/elb.port: '80'
          kubernetes.io/elb.autocreate:
            '{
                "type": "public",
                "bandwidth_name": "cce-bandwidth-******",
                "bandwidth_chargemode": "bandwidth",
                "bandwidth_size": 5,
                "bandwidth_sharetype": "PER",
                "eip_type": "5_bgp",
                "available_zone": [
                    "eu-de-01"
                ],
                "l7_flavor_name": "L7_flavor.elb.s1.small"
             }'
      spec:
        rules:
        - host: ''
          http:
            paths:
            - path: '/'
              backend:
                serviceName: <your_service_name>  # Replace it with the name of your target Service.
                servicePort: 80
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH

   .. table:: **Table 1** Key parameters

      +-------------------------------------------+-----------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                 | Mandatory                               | Type                  | Description                                                                                                                                                                                                                             |
      +===========================================+=========================================+=======================+=========================================================================================================================================================================================================================================+
      | kubernetes.io/elb.class                   | Yes                                     | String                | Select a proper load balancer type.                                                                                                                                                                                                     |
      |                                           |                                         |                       |                                                                                                                                                                                                                                         |
      |                                           |                                         |                       | The value can be:                                                                                                                                                                                                                       |
      |                                           |                                         |                       |                                                                                                                                                                                                                                         |
      |                                           |                                         |                       | -  **union**: shared load balancer                                                                                                                                                                                                      |
      |                                           |                                         |                       | -  **performance**: dedicated load balancer..                                                                                                                                                                                           |
      |                                           |                                         |                       |                                                                                                                                                                                                                                         |
      |                                           |                                         |                       | Default: **union**                                                                                                                                                                                                                      |
      +-------------------------------------------+-----------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/ingress.class               | Yes                                     | String                | **cce**: The self-developed ELB ingress is used.                                                                                                                                                                                        |
      |                                           |                                         |                       |                                                                                                                                                                                                                                         |
      |                                           | (only for clusters of v1.21 or earlier) |                       | This parameter is mandatory when an ingress is created by calling the API.                                                                                                                                                              |
      +-------------------------------------------+-----------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | ingressClassName                          | Yes                                     | String                | **cce**: The self-developed ELB ingress is used.                                                                                                                                                                                        |
      |                                           |                                         |                       |                                                                                                                                                                                                                                         |
      |                                           | (only for clusters of v1.23 or later)   |                       | This parameter is mandatory when an ingress is created by calling the API.                                                                                                                                                              |
      +-------------------------------------------+-----------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.port                    | Yes                                     | Integer               | This parameter indicates the external port registered with the address of the LoadBalancer Service.                                                                                                                                     |
      |                                           |                                         |                       |                                                                                                                                                                                                                                         |
      |                                           |                                         |                       | Supported range: 1 to 65535                                                                                                                                                                                                             |
      +-------------------------------------------+-----------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.subnet-id               | ``-``                                   | String                | ID of the subnet where the cluster is located. The value can contain 1 to 100 characters.                                                                                                                                               |
      |                                           |                                         |                       |                                                                                                                                                                                                                                         |
      |                                           |                                         |                       | -  Mandatory when a cluster of v1.11.7-r0 or earlier is to be automatically created.                                                                                                                                                    |
      |                                           |                                         |                       | -  Optional for clusters later than v1.11.7-r0. It is left blank by default.                                                                                                                                                            |
      +-------------------------------------------+-----------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.autocreate              | Yes                                     | elb.autocreate object | Whether to automatically create a load balancer associated with an ingress. For details about the field description, see :ref:`Table 2 <cce_10_0252__table268711532210>`.                                                               |
      |                                           |                                         |                       |                                                                                                                                                                                                                                         |
      |                                           |                                         |                       | **Example**                                                                                                                                                                                                                             |
      |                                           |                                         |                       |                                                                                                                                                                                                                                         |
      |                                           |                                         |                       | -  If a public network load balancer will be automatically created, set this parameter to the following value:                                                                                                                          |
      |                                           |                                         |                       |                                                                                                                                                                                                                                         |
      |                                           |                                         |                       |    {"type":"public","bandwidth_name":"cce-bandwidth-``******``","bandwidth_chargemode":"bandwidth","bandwidth_size":5,"bandwidth_sharetype":"PER","eip_type":"5_bgp","name":"james"}                                                    |
      |                                           |                                         |                       |                                                                                                                                                                                                                                         |
      |                                           |                                         |                       | -  If a private network load balancer will be automatically created, set this parameter to the following value:                                                                                                                         |
      |                                           |                                         |                       |                                                                                                                                                                                                                                         |
      |                                           |                                         |                       |    {"type":"inner","name":"A-location-d-test"}                                                                                                                                                                                          |
      +-------------------------------------------+-----------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | host                                      | No                                      | String                | Domain name for accessing the Service. By default, this parameter is left blank, and the domain name needs to be fully matched.                                                                                                         |
      +-------------------------------------------+-----------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | path                                      | Yes                                     | String                | User-defined route path. All external access requests must match **host** and **path**.                                                                                                                                                 |
      +-------------------------------------------+-----------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | serviceName                               | Yes                                     | String                | Name of the target Service bound to the ingress.                                                                                                                                                                                        |
      +-------------------------------------------+-----------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | servicePort                               | Yes                                     | Integer               | Access port of the target Service.                                                                                                                                                                                                      |
      +-------------------------------------------+-----------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | ingress.beta.kubernetes.io/url-match-mode | No                                      | String                | Route matching policy.                                                                                                                                                                                                                  |
      |                                           |                                         |                       |                                                                                                                                                                                                                                         |
      |                                           |                                         |                       | Default: **STARTS_WITH** (prefix match)                                                                                                                                                                                                 |
      |                                           |                                         |                       |                                                                                                                                                                                                                                         |
      |                                           |                                         |                       | Options:                                                                                                                                                                                                                                |
      |                                           |                                         |                       |                                                                                                                                                                                                                                         |
      |                                           |                                         |                       | -  **EQUAL_TO**: exact match                                                                                                                                                                                                            |
      |                                           |                                         |                       | -  **STARTS_WITH**: prefix match                                                                                                                                                                                                        |
      |                                           |                                         |                       | -  **REGEX**: regular expression match                                                                                                                                                                                                  |
      +-------------------------------------------+-----------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | pathType                                  | Yes                                     | String                | Path type. This field is supported only by clusters of v1.23 or later.                                                                                                                                                                  |
      |                                           |                                         |                       |                                                                                                                                                                                                                                         |
      |                                           |                                         |                       | -  **ImplementationSpecific**: The matching method depends on Ingress Controller. The matching method defined by **ingress.beta.kubernetes.io/url-match-mode** is used in CCE.                                                          |
      |                                           |                                         |                       | -  **Exact**: exact matching of the URL, which is case-sensitive.                                                                                                                                                                       |
      |                                           |                                         |                       | -  **Prefix**: matching based on the URL prefix separated by a slash (/). The match is case-sensitive, and elements in the path are matched one by one. A path element refers to a list of labels in the path separated by a slash (/). |
      +-------------------------------------------+-----------------------------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_10_0252__table268711532210:

   .. table:: **Table 2** Data structure of the elb.autocreate field

      +----------------------+---------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter            | Mandatory                             | Type            | Description                                                                                                                                                                                   |
      +======================+=======================================+=================+===============================================================================================================================================================================================+
      | type                 | No                                    | String          | Network type of the load balancer.                                                                                                                                                            |
      |                      |                                       |                 |                                                                                                                                                                                               |
      |                      |                                       |                 | -  **public**: public network load balancer                                                                                                                                                   |
      |                      |                                       |                 | -  **inner**: private network load balancer                                                                                                                                                   |
      |                      |                                       |                 |                                                                                                                                                                                               |
      |                      |                                       |                 | Default: **inner**                                                                                                                                                                            |
      +----------------------+---------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | bandwidth_name       | Yes for public network load balancers | String          | Bandwidth name. The default value is **cce-bandwidth-*****\***.                                                                                                                               |
      |                      |                                       |                 |                                                                                                                                                                                               |
      |                      |                                       |                 | Value range: a string of 1 to 64 characters, including lowercase letters, digits, and underscores (_). The value must start with a lowercase letter and end with a lowercase letter or digit. |
      +----------------------+---------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | bandwidth_chargemode | No                                    | String          | Bandwidth mode.                                                                                                                                                                               |
      +----------------------+---------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | bandwidth_size       | Yes for public network load balancers | Integer         | Bandwidth size. The value ranges from 1 Mbit/s to 2000 Mbit/s by default. The actual range varies depending on the configuration in each region.                                              |
      |                      |                                       |                 |                                                                                                                                                                                               |
      |                      |                                       |                 | -  The minimum increment for bandwidth adjustment varies depending on the bandwidth range. The details are as follows:                                                                        |
      |                      |                                       |                 |                                                                                                                                                                                               |
      |                      |                                       |                 |    -  The minimum increment is 1 Mbit/s if the allowed bandwidth ranges from 0 Mbit/s to 300 Mbit/s (with 300 Mbit/s included).                                                               |
      |                      |                                       |                 |    -  The minimum increment is 50 Mbit/s if the allowed bandwidth ranges from 300 Mbit/s to 1000 Mbit/s.                                                                                      |
      |                      |                                       |                 |    -  The minimum increment is 500 Mbit/s if the allowed bandwidth is greater than 1000 Mbit/s.                                                                                               |
      +----------------------+---------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | bandwidth_sharetype  | Yes for public network load balancers | String          | Bandwidth type.                                                                                                                                                                               |
      |                      |                                       |                 |                                                                                                                                                                                               |
      |                      |                                       |                 | **PER**: dedicated bandwidth.                                                                                                                                                                 |
      +----------------------+---------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | eip_type             | Yes for public network load balancers | String          | EIP type.                                                                                                                                                                                     |
      |                      |                                       |                 |                                                                                                                                                                                               |
      |                      |                                       |                 | -  **5_bgp**: dynamic BGP                                                                                                                                                                     |
      |                      |                                       |                 | -  **5_sbgp**: static BGP                                                                                                                                                                     |
      +----------------------+---------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | name                 | No                                    | String          | Name of the automatically created load balancer.                                                                                                                                              |
      |                      |                                       |                 |                                                                                                                                                                                               |
      |                      |                                       |                 | Value range: a string of 1 to 64 characters, including lowercase letters, digits, and underscores (_). The value must start with a lowercase letter and end with a lowercase letter or digit. |
      |                      |                                       |                 |                                                                                                                                                                                               |
      |                      |                                       |                 | Default: **cce-lb+ingress.UID**                                                                                                                                                               |
      +----------------------+---------------------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Create an ingress.

   **kubectl create -f ingress-test.yaml**

   If information similar to the following is displayed, the ingress has been created.

   .. code-block::

      ingress/ingress-test created

   **kubectl get ingress**

   If information similar to the following is displayed, the ingress has been created successfully and the workload is accessible.

   .. code-block::

      NAME             HOSTS     ADDRESS          PORTS   AGE
      ingress-test     *         121.**.**.**     80      10s

#. Enter **http://121.**.**.*\*:80** in the address box of the browser to access the workload (for example, :ref:`Nginx workload <cce_10_0047__section155246177178>`).

   **121.**.**.*\*** indicates the IP address of the unified load balancer.

.. _cce_10_0252__section32300431736:

Creating an Ingress - Interconnecting with an Existing Load Balancer
--------------------------------------------------------------------

CCE allows you to connect to an existing load balancer when creating an ingress.

.. note::

   -  Existing dedicated load balancers must be the application type (HTTP/HTTPS) supporting private networks (with a private IP).

**If the cluster version is 1.23 or earlier, the YAML file configuration is as follows:**

.. code-block::

   apiVersion: networking.k8s.io/v1
   kind: Ingress
   metadata:
     name: ingress-test
     annotations:
       kubernetes.io/elb.id: <your_elb_id>  # Replace it with the ID of your existing load balancer.
       kubernetes.io/elb.ip: <your_elb_ip>  # Replace it with your existing load balancer IP.
       kubernetes.io/elb.port: '80'
   spec:
     rules:
     - host: ''
       http:
         paths:
         - path: '/'
           backend:
             service:
               name: <your_service_name>  # Replace it with the name of your target Service.
               port:
                 number: 8080             # Replace 8080 with your target service port number.
           property:
             ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
           pathType: ImplementationSpecific
     ingressClassName: cce

**If the cluster version is 1.21 or later, the YAML file configuration is as follows:**

.. code-block::

   apiVersion: networking.k8s.io/v1beta1
   kind: Ingress
   metadata:
     name: ingress-test
     annotations:
       kubernetes.io/elb.id: <your_elb_id>  # Replace it with the ID of your existing load balancer.
       kubernetes.io/elb.ip: <your_elb_ip>  # Replace it with your existing load balancer IP.
       kubernetes.io/elb.port: '80'
       kubernetes.io/ingress.class: cce
   spec:
     rules:
     - host: ''
       http:
         paths:
         - path: '/'
           backend:
             serviceName: <your_service_name>  # Replace it with the name of your target Service.
             servicePort: 80
           property:
             ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH

.. table:: **Table 3** Key parameters

   +----------------------+-----------------+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter            | Mandatory       | Type            | Description                                                                                                                                                                                             |
   +======================+=================+=================+=========================================================================================================================================================================================================+
   | kubernetes.io/elb.id | Yes             | String          | This parameter indicates the ID of a load balancer. The value can contain 1 to 100 characters.                                                                                                          |
   |                      |                 |                 |                                                                                                                                                                                                         |
   |                      |                 |                 | **How to obtain**:                                                                                                                                                                                      |
   |                      |                 |                 |                                                                                                                                                                                                         |
   |                      |                 |                 | On the management console, click **Service List**, and choose **Networking** > **Elastic Load Balance**. Click the name of the target load balancer. On the **Summary** tab page, find and copy the ID. |
   +----------------------+-----------------+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | kubernetes.io/elb.ip | Yes             | String          | This parameter indicates the service address of a load balancer. The value can be the public IP address of a public network load balancer or the private IP address of a private network load balancer. |
   +----------------------+-----------------+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Configuring HTTPS Certificates
------------------------------

Ingress supports TLS certificate configuration and secures your Services with HTTPS.

.. note::

   If HTTPS is enabled for the same port of the same load balancer of multiple ingresses, you must select the same certificate.

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Run the following command to create a YAML file named **ingress-test-secret.yaml** (the file name can be customized):

   **vi ingress-test-secret.yaml**

   **The YAML file is configured as follows:**

   .. code-block::

      apiVersion: v1
      data:
        tls.crt: LS0******tLS0tCg==
        tls.key: LS0tL******0tLS0K
      kind: Secret
      metadata:
        annotations:
          description: test for ingressTLS secrets
        name: ingress-test-secret
        namespace: default
      type: IngressTLS

   .. note::

      In the preceding information, **tls.crt** and **tls.key** are only examples. Replace them with the actual files. The values of **tls.crt** and **tls.key** are Base64-encoded.

#. Create a secret.

   **kubectl create -f ingress-test-secret.yaml**

   If information similar to the following is displayed, the secret is being created:

   .. code-block::

      secret/ingress-test-secret created

   View the created secrets.

   **kubectl get secrets**

   If information similar to the following is displayed, the secret has been created successfully:

   .. code-block::

      NAME                         TYPE                                  DATA      AGE
      ingress-test-secret          IngressTLS                            2         13s

#. Create a YAML file named **ingress-test.yaml**. The file name can be customized.

   **vi ingress-test.yaml**

   .. note::

      Default security policy (kubernetes.io/elb.tls-ciphers-policy) is supported only in clusters of v1.17.17 or later.

   **The following uses the automatically created load balancer as an example. The YAML file is configured as follows:**

   **For clusters of v1.21 or earlier:**

   .. code-block::

      apiVersion: networking.k8s.io/v1beta1
      kind: Ingress
      metadata:
        name: ingress-test
        annotations:
          kubernetes.io/elb.class: union
          kubernetes.io/ingress.class: cce
          kubernetes.io/elb.port: '443'
          kubernetes.io/elb.autocreate:
            '{
                "type":"public",
                "bandwidth_name":"cce-bandwidth-15511633796**",
                "bandwidth_chargemode":"bandwidth",
                "bandwidth_size":5,
                "bandwidth_sharetype":"PER",
                "eip_type":"5_bgp"
              }'
          kubernetes.io/elb.tls-ciphers-policy: tls-1-2
      spec:
        tls:
        - secretName: ingress-test-secret
        rules:
        - host: ''
          http:
            paths:
            - path: '/'
              backend:
                serviceName: <your_service_name>  # Replace it with the name of your target Service.
                servicePort: 80
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH

   **For clusters of v1.23 or later:**

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        name: ingress-test
        annotations:
          kubernetes.io/elb.class: union
          kubernetes.io/elb.port: '443'
          kubernetes.io/elb.autocreate:
            '{
                "type":"public",
                "bandwidth_name":"cce-bandwidth-15511633796**",
                "bandwidth_chargemode":"bandwidth",
                "bandwidth_size":5,
                "bandwidth_sharetype":"PER",
                "eip_type":"5_bgp"
              }'
          kubernetes.io/elb.tls-ciphers-policy: tls-1-2
      spec:
        tls:
        - secretName: ingress-test-secret
        rules:
        - host: ''
          http:
            paths:
            - path: '/'
              backend:
                service:
                  name: <your_service_name>  # Replace it with the name of your target Service.
                  port:
                    number: 8080             # Replace 8080 with the port number of your target Service.
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
              pathType: ImplementationSpecific
        ingressClassName: cce

   .. table:: **Table 4** Key parameters

      +--------------------------------------+-----------------+------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                            | Mandatory       | Type             | Description                                                                                                                                                                                                                                |
      +======================================+=================+==================+============================================================================================================================================================================================================================================+
      | kubernetes.io/elb.tls-ciphers-policy | No              | String           | The default value is **tls-1-2**, which is the default security policy used by the listener and takes effect only when the HTTPS protocol is used.                                                                                         |
      |                                      |                 |                  |                                                                                                                                                                                                                                            |
      |                                      |                 |                  | Options:                                                                                                                                                                                                                                   |
      |                                      |                 |                  |                                                                                                                                                                                                                                            |
      |                                      |                 |                  | -  tls-1-0                                                                                                                                                                                                                                 |
      |                                      |                 |                  | -  tls-1-1                                                                                                                                                                                                                                 |
      |                                      |                 |                  | -  tls-1-2                                                                                                                                                                                                                                 |
      |                                      |                 |                  | -  tls-1-2-strict                                                                                                                                                                                                                          |
      |                                      |                 |                  |                                                                                                                                                                                                                                            |
      |                                      |                 |                  | For details of cipher suites for each security policy, see :ref:`Table 5 <cce_10_0252__table9419191416246>`.                                                                                                                               |
      +--------------------------------------+-----------------+------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | tls                                  | No              | Array of strings | This parameter is mandatory if HTTPS is used. Multiple independent domain names and certificates can be added to this parameter. For details, see :ref:`Configuring the Server Name Indication (SNI) <cce_10_0252__section0555194782414>`. |
      +--------------------------------------+-----------------+------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | secretName                           | No              | String           | This parameter is mandatory if HTTPS is used. Set this parameter to the name of the created secret.                                                                                                                                        |
      +--------------------------------------+-----------------+------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_10_0252__table9419191416246:

   .. table:: **Table 5** tls_ciphers_policy parameter description

      +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Security Policy       | TLS Version           | Cipher Suite                                                                                                                                                                                                                                                                                                                                                                                          |
      +=======================+=======================+=======================================================================================================================================================================================================================================================================================================================================================================================================+
      | tls-1-0               | TLS 1.2               | ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-GCM-SHA256:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:AES128-SHA256:AES256-SHA256:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES128-SHA:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:AES128-SHA:AES256-SHA |
      |                       |                       |                                                                                                                                                                                                                                                                                                                                                                                                       |
      |                       | TLS 1.1               |                                                                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                       |                                                                                                                                                                                                                                                                                                                                                                                                       |
      |                       | TLS 1.0               |                                                                                                                                                                                                                                                                                                                                                                                                       |
      +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | tls-1-1               | TLS 1.2               |                                                                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                       |                                                                                                                                                                                                                                                                                                                                                                                                       |
      |                       | TLS 1.1               |                                                                                                                                                                                                                                                                                                                                                                                                       |
      +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | tls-1-2               | TLS 1.2               |                                                                                                                                                                                                                                                                                                                                                                                                       |
      +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | tls-1-2-strict        | TLS 1.2               | ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-GCM-SHA256:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:AES128-SHA256:AES256-SHA256:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA384                                                                                                               |
      +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Create an ingress.

   **kubectl create -f ingress-test.yaml**

   If information similar to the following is displayed, the ingress has been created.

   .. code-block::

      ingress/ingress-test created

   View the created ingress.

   **kubectl get ingress**

   If information similar to the following is displayed, the ingress has been created successfully and the workload is accessible.

   .. code-block::

      NAME             HOSTS     ADDRESS          PORTS   AGE
      ingress-test     *         121.**.**.**     80      10s

#. Enter **https://121.**.**.*\*:443** in the address box of the browser to access the workload (for example, :ref:`Nginx workload <cce_10_0047__section155246177178>`).

   **121.**.**.*\*** indicates the IP address of the unified load balancer.

Using HTTP/2
------------

Ingresses can use HTTP/2 to expose services. Connections from the load balancer proxy to your applications use HTTP/1.X by default. If your application is capable of receiving HTTP/2 requests, you can add the following field to the ingress annotation to enable the use of HTTP/2:

\`kubernetes.io/elb.http2-enable: 'true'\`

The following shows the YAML file for associating with an existing load balancer:

**For clusters of v1.21 or earlier:**

.. code-block::

   apiVersion: networking.k8s.io/v1beta1
   kind: Ingress
   metadata:
     name: ingress-test
     annotations:
       kubernetes.io/elb.id: <your_elb_id>  # Replace it with the ID of your existing load balancer.
       kubernetes.io/elb.ip: <your_elb_ip>  # Replace it with the IP of your existing load balancer.
       kubernetes.io/elb.port: '443'
       kubernetes.io/ingress.class: cce
       kubernetes.io/elb.http2-enable: 'true' # Enable HTTP/2.
   spec:
     tls:
     - secretName: ingress-test-secret
     rules:
     - host: ''
       http:
         paths:
         - path: '/'
           backend:
             serviceName: <your_service_name>  # Replace it with the name of your target Service.
             servicePort: 80                   # Replace it with the port number of your target Service.
           property:
             ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH

**For clusters of v1.23 or later:**

.. code-block::

   apiVersion: networking.k8s.io/v1
   kind: Ingress
   metadata:
     name: ingress-test
     annotations:
       kubernetes.io/elb.id: <your_elb_id>  # Replace it with the ID of your existing load balancer.
       kubernetes.io/elb.ip: <your_elb_ip>  # Replace it with the IP of your existing load balancer.
       kubernetes.io/elb.port: '443'
       kubernetes.io/elb.http2-enable: 'true' # Enable HTTP/2.
   spec:
     tls:
     - secretName: ingress-test-secret
     rules:
     - host: ''
       http:
         paths:
         - path: '/'
           backend:
             service:
               name: <your_service_name>  # Replace it with the name of your target Service.
               port:
                 number: 8080             # Replace 8080 with the port number of your target Service.
           property:
             ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
           pathType: ImplementationSpecific
     ingressClassName: cce

Table 6 HTTP/2 parameters

+--------------------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Parameter                      | Mandatory       | Type            | Description                                                                                                                                                                                                                                                                                                                      |
+================================+=================+=================+==================================================================================================================================================================================================================================================================================================================================+
| kubernetes.io/elb.http2-enable | No              | Bool            | Whether HTTP/2 is enabled. Request forwarding using HTTP/2 improves the access performance between your application and the load balancer. However, the load balancer still uses HTTP 1.X to forward requests to the backend server. **This parameter is supported in clusters of v1.19.16-r0, v1.21.3-r0, and later versions.** |
|                                |                 |                 |                                                                                                                                                                                                                                                                                                                                  |
|                                |                 |                 | Options:                                                                                                                                                                                                                                                                                                                         |
|                                |                 |                 |                                                                                                                                                                                                                                                                                                                                  |
|                                |                 |                 | -  **true**: enabled                                                                                                                                                                                                                                                                                                             |
|                                |                 |                 | -  **false**: disabled (default value)                                                                                                                                                                                                                                                                                           |
|                                |                 |                 |                                                                                                                                                                                                                                                                                                                                  |
|                                |                 |                 | Note: **HTTP/2 can be enabled or disabled only when the listener uses HTTPS.** This parameter is invalid and defaults to **false** when the listener protocol is HTTP.                                                                                                                                                           |
+--------------------------------+-----------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_10_0252__section0555194782414:

Configuring the Server Name Indication (SNI)
--------------------------------------------

SNI allows multiple TLS-based access domain names to be provided for external systems using the same IP address and port number. Different domain names can use different security certificates.

.. note::

   -  Only one domain name can be specified for each SNI certificate. Wildcard-domain certificates are supported.
   -  Security policy (kubernetes.io/elb.tls-ciphers-policy) is supported only in clusters of v1.17.11 or later.

You can enable SNI when the preceding conditions are met. The following uses the automatic creation of a load balancer as an example. In this example, **sni-test-secret-1** and **sni-test-secret-2** are SNI certificates. The domain names specified by the certificates must be the same as those in the certificates.

**For clusters of v1.21 or earlier:**

.. code-block::

   apiVersion: networking.k8s.io/v1beta1
   kind: Ingress
   metadata:
     name: ingress-test
     annotations:
       kubernetes.io/elb.class: union
       kubernetes.io/ingress.class: cce
       kubernetes.io/elb.port: '443'
       kubernetes.io/elb.autocreate:
         '{
             "type":"public",
             "bandwidth_name":"cce-bandwidth-******",
             "bandwidth_chargemode":"bandwidth",
             "bandwidth_size":5,
             "bandwidth_sharetype":"PER",
             "eip_type":"5_bgp"
           }'
       kubernetes.io/elb.tls-ciphers-policy: tls-1-2
   spec:
     tls:
     - secretName: ingress-test-secret
     - hosts:
         - example.top  # Domain name specified a certificate is issued
       secretName: sni-test-secret-1
     - hosts:
         - example.com  # Domain name specified a certificate is issued
       secretName: sni-test-secret-2
     rules:
     - host: ''
       http:
         paths:
         - path: '/'
           backend:
             serviceName: <your_service_name>  # Replace it with the name of your target Service.
             servicePort: 80
           property:
             ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH

**For clusters of v1.23 or later:**

.. code-block::

   apiVersion: networking.k8s.io/v1
   kind: Ingress
   metadata:
     name: ingress-test
     annotations:
       kubernetes.io/elb.class: union
       kubernetes.io/elb.port: '443'
       kubernetes.io/elb.autocreate:
         '{
             "type":"public",
             "bandwidth_name":"cce-bandwidth-******",
             "bandwidth_chargemode":"bandwidth",
             "bandwidth_size":5,
             "bandwidth_sharetype":"PER",
             "eip_type":"5_bgp"
           }'
       kubernetes.io/elb.tls-ciphers-policy: tls-1-2
   spec:
     tls:
     - secretName: ingress-test-secret
     - hosts:
         - example.top  # Domain name specified a certificate is issued
       secretName: sni-test-secret-1
     - hosts:
         - example.com  # Domain name specified a certificate is issued
       secretName: sni-test-secret-2
     rules:
     - host: ''
       http:
         paths:
         - path: '/'
           backend:
             service:
               name: <your_service_name>  # Replace it with the name of your target Service.
               port:
                 number: 8080             # Replace 8080 with the port number of your target Service.
           property:
             ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
           pathType: ImplementationSpecific
     ingressClassName: cce

Accessing Multiple Services
---------------------------

Ingresses can route requests to multiple backend Services based on different matching policies. The **spec** field in the YAML file is set as below. You can access **www.example.com/foo**, **www.example.com/bar**, and **foo.example.com/** to route to three different backend Services.

.. important::

   The URL registered in an ingress forwarding policy must be the same as the URL exposed by the backend Service. Otherwise, a 404 error will be returned.

.. code-block::

   spec:
     rules:
     - host: 'www.example.com'
       http:
         paths:
         - path: '/foo'
           backend:
             serviceName: <your_service_name>  # Replace it with the name of your target Service.
             servicePort: 80
           property:
             ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
         - path: '/bar'
           backend:
             serviceName: <your_service_name>  # Replace it with the name of your target Service.
             servicePort: 80
           property:
             ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
     - host: 'foo.example.com'
       http:
         paths:
         - path: '/'
           backend:
             serviceName: <your_service_name>  # Replace it with the name of your target Service.
             servicePort: 80
           property:
             ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH

.. |image1| image:: /_static/images/en-us_image_0000001276433425.png
