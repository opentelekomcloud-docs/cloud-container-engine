:original_name: cce_10_0252.html

.. _cce_10_0252:

Creating a LoadBalancer Ingress Using kubectl
=============================================

This section uses an :ref:`Nginx workload <cce_10_0047__section155246177178>` as an example to describe how to create a LoadBalancer ingress using kubectl.

-  If no load balancer is available in the same VPC, CCE can automatically create a load balancer when creating an ingress. For details, see :ref:`Automatically Creating a Load Balancer While Creating an Ingress <cce_10_0252__section3675115714214>`.
-  If a load balancer is available in the same VPC, perform the operation by referring to :ref:`Associating an Existing Load Balancer to an Ingress While Creating the Ingress <cce_10_0252__section32300431736>`.

.. _cce_10_0252__section8926910153415:

Ingress API Version Upgrade in CCE Clusters v1.23
-------------------------------------------------

In CCE clusters of v1.23 or later, the ingress version is switched to **networking.k8s.io/v1**.

Compared with v1beta1, v1 has the following differences in parameters:

-  The ingress type is specified by **spec.ingressClassName** instead of **kubernetes.io/ingress.class** in **annotations**.
-  The format of **backend** has changed.
-  The **pathType** parameter must be specified for each path. The options are as follows:

   -  **ImplementationSpecific**: The matching method depends on Ingress Controller. The matching method defined by **ingress.beta.kubernetes.io/url-match-mode** is used in CCE, which is the same as v1beta1.
   -  **Exact**: exact matching of the URL, which is case-sensitive.
   -  **Prefix**: matching based on the URL prefix separated by a slash (/). The match is case-sensitive, and elements in the path are matched one by one. A path element refers to a list of labels in the path separated by a slash (/).

|image1|

Prerequisites
-------------

-  An available workload has been deployed in the cluster for external access. If no workload is available, deploy a workload by referring to :ref:`Creating a Deployment <cce_10_0047>`, :ref:`Creating a StatefulSet <cce_10_0048>`, or :ref:`Creating a DaemonSet <cce_10_0216>`.
-  A Service for external access has been configured for the workload. :ref:`Services Supported by LoadBalancer Ingresses <cce_10_0094__section3565202819276>` lists the Service types supported by LoadBalancer ingresses.
-  A dedicated load balancer must be of the application type (HTTP/HTTPS) and support private networks (with a private IP address).

Creating an Ingress Using kubectl
---------------------------------

You can determine whether to automatically create a load balancer or use an existing one when creating an ingress.

.. _cce_10_0252__section3675115714214:

Automatically Creating a Load Balancer While Creating an Ingress
----------------------------------------------------------------

The following describes how to run the kubectl command to automatically create a load balancer when creating an ingress.

#. Use kubectl to access the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create a YAML file named **ingress-test.yaml**. The file name can be customized.

   .. code-block::

      vi ingress-test.yaml

   .. note::

      Starting from cluster v1.23, the ingress version is switched from **networking.k8s.io/v1beta1** to **networking.k8s.io/v1**. For details about the differences between v1 and v1beta1, see :ref:`Ingress API Version Upgrade in CCE Clusters v1.23 <cce_10_0252__section8926910153415>`.

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
                "bandwidth_chargemode":"traffic",
                "bandwidth_size":5,
                "bandwidth_sharetype":"PER",
                "vip_subnet_cidr_id": "*****",
                "vip_address": "**.**.**.**",
                "eip_type":"5_bgp"
              }'
          kubernetes.io/elb.tags: key1=value1,key2=value2           # ELB resource tags
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
                    number: <your_service_port>  # Replace it with the port number of your target Service.
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
              pathType: ImplementationSpecific
        ingressClassName: cce    # A LoadBalancer ingress is used.

   **Example of a shared load balancer (public network access) for clusters of v1.21 or earlier:**

   .. code-block::

      apiVersion: networking.k8s.io/v1beta1
      kind: Ingress
      metadata:
        name: ingress-test
        annotations:
          kubernetes.io/elb.class: union
          kubernetes.io/ingress.class: cce    # A LoadBalancer ingress is used.
          kubernetes.io/elb.port: '80'
          kubernetes.io/elb.autocreate:
            '{
                "type":"public",
                "bandwidth_name":"cce-bandwidth-******",
                "bandwidth_chargemode":"traffic",
                "bandwidth_size":5,
                "bandwidth_sharetype":"PER",
                "eip_type":"5_bgp"
              }'
          kubernetes.io/elb.tags: key1=value1,key2=value2           # ELB resource tags
      spec:
        rules:
        - host: ''
          http:
            paths:
            - path: '/'
              backend:
                serviceName: <your_service_name>  # Replace it with the name of your target Service.
                servicePort: <your_service_port>  # Replace it with the port number of your target Service.
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
                "bandwidth_chargemode": "traffic",
                "bandwidth_size": 5,
                "bandwidth_sharetype": "PER",
                "eip_type": "5_bgp",
                "vip_subnet_cidr_id": "*****",
                "vip_address": "**.**.**.**",
                "elb_virsubnet_ids":["*****"],
                "available_zone": [
                    "eu-de-01"
                ],
                "l7_flavor_name": "L7_flavor.elb.s1.small"
             }'
          kubernetes.io/elb.tags: key1=value1,key2=value2           # ELB resource tags
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
                    number: <your_service_port>  # Replace it with the port number of your target Service.
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
              pathType: ImplementationSpecific
        ingressClassName: cce

   **Example of a dedicated load balancer (public network access) for clusters of v1.21 or earlier:**

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
                "bandwidth_chargemode": "traffic",
                "bandwidth_size": 5,
                "bandwidth_sharetype": "PER",
                "eip_type": "5_bgp",
                "elb_virsubnet_ids":["*****"],
                "available_zone": [
                    "eu-de-01"
                ],
                "l7_flavor_name": "L7_flavor.elb.s1.small"
             }'
          kubernetes.io/elb.tags: key1=value1,key2=value2           # ELB resource tags
      spec:
        rules:
        - host: ''
          http:
            paths:
            - path: '/'
              backend:
                serviceName: <your_service_name>  # Replace it with the name of your target Service.
                servicePort: <your_service_port>  # Replace it with the port number of your target Service.
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH

   .. table:: **Table 1** Key parameters

      +-------------------------------------------+-----------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                 | Mandatory                               | Type                  | Description                                                                                                                                                                                                                                                                                                 |
      +===========================================+=========================================+=======================+=============================================================================================================================================================================================================================================================================================================+
      | kubernetes.io/elb.class                   | Yes                                     | String                | Select a proper load balancer type.                                                                                                                                                                                                                                                                         |
      |                                           |                                         |                       |                                                                                                                                                                                                                                                                                                             |
      |                                           |                                         |                       | -  **union**: shared load balancer                                                                                                                                                                                                                                                                          |
      |                                           |                                         |                       | -  **performance**: dedicated load balancer                                                                                                                                                                                                                                                                 |
      +-------------------------------------------+-----------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/ingress.class               | Yes                                     | String                | **cce**: A proprietary LoadBalancer ingress is used.                                                                                                                                                                                                                                                        |
      |                                           |                                         |                       |                                                                                                                                                                                                                                                                                                             |
      |                                           | (only for clusters of v1.21 or earlier) |                       | This parameter is mandatory when an ingress is created by calling the API.                                                                                                                                                                                                                                  |
      +-------------------------------------------+-----------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | ingressClassName                          | Yes                                     | String                | **cce**: A proprietary LoadBalancer ingress is used.                                                                                                                                                                                                                                                        |
      |                                           |                                         |                       |                                                                                                                                                                                                                                                                                                             |
      |                                           | (only for clusters of v1.23 or later)   |                       | This parameter is mandatory when an ingress is created by calling the API.                                                                                                                                                                                                                                  |
      +-------------------------------------------+-----------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.port                    | Yes                                     | String                | This parameter indicates the external port registered with the address of the LoadBalancer Service.                                                                                                                                                                                                         |
      |                                           |                                         |                       |                                                                                                                                                                                                                                                                                                             |
      |                                           |                                         |                       | The value ranges from 1 to 65535.                                                                                                                                                                                                                                                                           |
      |                                           |                                         |                       |                                                                                                                                                                                                                                                                                                             |
      |                                           |                                         |                       | .. note::                                                                                                                                                                                                                                                                                                   |
      |                                           |                                         |                       |                                                                                                                                                                                                                                                                                                             |
      |                                           |                                         |                       |    Some ports are high-risk ports and are blocked by default, for example, port 21.                                                                                                                                                                                                                         |
      +-------------------------------------------+-----------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.subnet-id               | None                                    | String                | ID of the subnet where the cluster is located. The value can contain 1 to 100 characters.                                                                                                                                                                                                                   |
      |                                           |                                         |                       |                                                                                                                                                                                                                                                                                                             |
      |                                           |                                         |                       | -  Mandatory when a cluster of v1.11.7-r0 or earlier is to be automatically created.                                                                                                                                                                                                                        |
      |                                           |                                         |                       | -  Optional for clusters later than v1.11.7-r0. It is left blank by default.                                                                                                                                                                                                                                |
      +-------------------------------------------+-----------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.autocreate              | Yes                                     | elb.autocreate object | Whether to automatically create a load balancer associated with an ingress. For details about the field description, see :ref:`Table 2 <cce_10_0252__table268711532210>`.                                                                                                                                   |
      |                                           |                                         |                       |                                                                                                                                                                                                                                                                                                             |
      |                                           |                                         |                       | **Example**                                                                                                                                                                                                                                                                                                 |
      |                                           |                                         |                       |                                                                                                                                                                                                                                                                                                             |
      |                                           |                                         |                       | -  Automatically created shared load balancer with an EIP bound:                                                                                                                                                                                                                                            |
      |                                           |                                         |                       |                                                                                                                                                                                                                                                                                                             |
      |                                           |                                         |                       |    '{"type":"public","bandwidth_name":"cce-bandwidth-``******``","bandwidth_chargemode":"traffic","bandwidth_size":5,"bandwidth_sharetype":"PER","eip_type":"5_bgp","name":"james"}'                                                                                                                        |
      |                                           |                                         |                       |                                                                                                                                                                                                                                                                                                             |
      |                                           |                                         |                       | -  Automatically created shared load balancer:                                                                                                                                                                                                                                                              |
      |                                           |                                         |                       |                                                                                                                                                                                                                                                                                                             |
      |                                           |                                         |                       |    {"type":"inner","name":"A-location-d-test"}                                                                                                                                                                                                                                                              |
      +-------------------------------------------+-----------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.tags                    | No                                      | String                | Whether to add resource tags to a load balancer. This function is available only when the load balancer is automatically created, and the cluster is of v1.23.11-r0, v1.25.6-r0, v1.27.3-r0, or later.                                                                                                      |
      |                                           |                                         |                       |                                                                                                                                                                                                                                                                                                             |
      |                                           |                                         |                       | A tag is in the format of "key=value". Use commas (,) to separate multiple tags.                                                                                                                                                                                                                            |
      +-------------------------------------------+-----------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | host                                      | No                                      | String                | Domain name for accessing the Service. By default, this parameter is left blank, and the domain name needs to be fully matched. Ensure that the domain name has been registered and licensed. Once a forwarding policy is configured with a domain name specified, you must use the domain name for access. |
      +-------------------------------------------+-----------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | path                                      | Yes                                     | String                | User-defined route path. All external access requests must match **host** and **path**.                                                                                                                                                                                                                     |
      |                                           |                                         |                       |                                                                                                                                                                                                                                                                                                             |
      |                                           |                                         |                       | .. note::                                                                                                                                                                                                                                                                                                   |
      |                                           |                                         |                       |                                                                                                                                                                                                                                                                                                             |
      |                                           |                                         |                       |    The access path added here must exist in the backend application. Otherwise, the forwarding fails.                                                                                                                                                                                                       |
      |                                           |                                         |                       |                                                                                                                                                                                                                                                                                                             |
      |                                           |                                         |                       |    For example, the default access URL of the Nginx application is **/usr/share/nginx/html**. When adding **/test** to the ingress forwarding policy, ensure the access URL of your Nginx application contains **/usr/share/nginx/html/test**. Otherwise, error 404 will be returned.                       |
      +-------------------------------------------+-----------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | ingress.beta.kubernetes.io/url-match-mode | No                                      | String                | Route matching policy.                                                                                                                                                                                                                                                                                      |
      |                                           |                                         |                       |                                                                                                                                                                                                                                                                                                             |
      |                                           |                                         |                       | Default: **STARTS_WITH** (prefix match)                                                                                                                                                                                                                                                                     |
      |                                           |                                         |                       |                                                                                                                                                                                                                                                                                                             |
      |                                           |                                         |                       | Options:                                                                                                                                                                                                                                                                                                    |
      |                                           |                                         |                       |                                                                                                                                                                                                                                                                                                             |
      |                                           |                                         |                       | -  **EQUAL_TO**: exact match                                                                                                                                                                                                                                                                                |
      |                                           |                                         |                       | -  **STARTS_WITH**: prefix match                                                                                                                                                                                                                                                                            |
      |                                           |                                         |                       | -  **REGEX**: regular expression match                                                                                                                                                                                                                                                                      |
      +-------------------------------------------+-----------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | pathType                                  | Yes                                     | String                | Path type. This field is supported only by clusters of v1.23 or later.                                                                                                                                                                                                                                      |
      |                                           |                                         |                       |                                                                                                                                                                                                                                                                                                             |
      |                                           |                                         |                       | -  **ImplementationSpecific**: The matching method depends on Ingress Controller. The matching method defined by **ingress.beta.kubernetes.io/url-match-mode** is used in CCE.                                                                                                                              |
      |                                           |                                         |                       | -  **Exact**: exact matching of the URL, which is case-sensitive.                                                                                                                                                                                                                                           |
      |                                           |                                         |                       | -  **Prefix**: prefix matching, which is case-sensitive. With this method, the URL path is separated into multiple elements by slashes (/) and the elements are matched one by one. If each element in the URL matches the path, the subpaths of the URL can be routed normally.                            |
      |                                           |                                         |                       |                                                                                                                                                                                                                                                                                                             |
      |                                           |                                         |                       |    .. note::                                                                                                                                                                                                                                                                                                |
      |                                           |                                         |                       |                                                                                                                                                                                                                                                                                                             |
      |                                           |                                         |                       |       -  During prefix matching, each element must be exactly matched. If the last element of the URL is the substring of the last element in the request path, no matching is performed. For example, **/foo/bar** matches **/foo/bar/baz** but does not match **/foo/barbaz**.                            |
      |                                           |                                         |                       |       -  When elements are separated by slashes (/), if the URL or request path ends with a slash (/), the slash (/) at the end is ignored. For example, **/foo/bar** matches **/foo/bar/**.                                                                                                                |
      |                                           |                                         |                       |                                                                                                                                                                                                                                                                                                             |
      |                                           |                                         |                       | See `examples <https://kubernetes.io/docs/concepts/services-networking/ingress/>`__ of ingress path matching.                                                                                                                                                                                               |
      +-------------------------------------------+-----------------------------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_10_0252__table268711532210:

   .. table:: **Table 2** elb.autocreate data structure

      +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter             | Mandatory                             | Type             | Description                                                                                                                                                                                                                                                                                                                                           |
      +=======================+=======================================+==================+=======================================================================================================================================================================================================================================================================================================================================================+
      | name                  | No                                    | String           | Name of the automatically created load balancer.                                                                                                                                                                                                                                                                                                      |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | The value can contain 1 to 64 characters. Only letters, digits, underscores (_), hyphens (-), and periods (.) are allowed.                                                                                                                                                                                                                            |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | Default: **cce-lb+service.UID**                                                                                                                                                                                                                                                                                                                       |
      +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | type                  | No                                    | String           | Network type of the load balancer.                                                                                                                                                                                                                                                                                                                    |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | -  **public**: public network load balancer                                                                                                                                                                                                                                                                                                           |
      |                       |                                       |                  | -  **inner**: private network load balancer                                                                                                                                                                                                                                                                                                           |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | Default: **inner**                                                                                                                                                                                                                                                                                                                                    |
      +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | bandwidth_name        | Yes for public network load balancers | String           | Bandwidth name. The default value is **cce-bandwidth-**\ ``******``.                                                                                                                                                                                                                                                                                  |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | The value can contain 1 to 64 characters. Only letters, digits, underscores (_), hyphens (-), and periods (.) are allowed.                                                                                                                                                                                                                            |
      +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | bandwidth_chargemode  | No                                    | String           | Bandwidth mode.                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | -  **traffic**: billed by traffic                                                                                                                                                                                                                                                                                                                     |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | Default: **traffic**                                                                                                                                                                                                                                                                                                                                  |
      +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | bandwidth_size        | Yes for public network load balancers | Integer          | Bandwidth size. The value ranges from 1 Mbit/s to 2000 Mbit/s by default. Configure this parameter based on the bandwidth range allowed in your region.                                                                                                                                                                                               |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | The minimum increment for bandwidth adjustment varies depending on the bandwidth range.                                                                                                                                                                                                                                                               |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | -  The minimum increment is 1 Mbit/s if the allowed bandwidth does not exceed 300 Mbit/s.                                                                                                                                                                                                                                                             |
      |                       |                                       |                  | -  The minimum increment is 50 Mbit/s if the allowed bandwidth ranges from 300 Mbit/s to 1000 Mbit/s.                                                                                                                                                                                                                                                 |
      |                       |                                       |                  | -  The minimum increment is 500 Mbit/s if the allowed bandwidth exceeds 1000 Mbit/s.                                                                                                                                                                                                                                                                  |
      +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | bandwidth_sharetype   | Yes for public network load balancers | String           | Bandwidth sharing mode.                                                                                                                                                                                                                                                                                                                               |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | -  **PER**: dedicated bandwidth                                                                                                                                                                                                                                                                                                                       |
      +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | eip_type              | Yes for public network load balancers | String           | EIP type.                                                                                                                                                                                                                                                                                                                                             |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | -  **5_bgp**: dynamic BGP                                                                                                                                                                                                                                                                                                                             |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | The specific type varies with regions. For details, see the EIP console.                                                                                                                                                                                                                                                                              |
      +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | vip_subnet_cidr_id    | No                                    | String           | Subnet where a load balancer is located. The subnet must belong to the VPC where the cluster resides.                                                                                                                                                                                                                                                 |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | If this parameter is not specified, the ELB load balancer and the cluster are in the same subnet.                                                                                                                                                                                                                                                     |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | This field can be specified only for clusters of v1.21 or later.                                                                                                                                                                                                                                                                                      |
      +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | vip_address           | No                                    | String           | Private IP address of the load balancer. Only IPv4 addresses are supported.                                                                                                                                                                                                                                                                           |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | The IP address must be in the ELB CIDR block. If this parameter is not specified, an IP address will be automatically assigned from the ELB CIDR block.                                                                                                                                                                                               |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | This parameter is available only in clusters of v1.23.11-r0, v1.25.6-r0, v1.27.3-r0, or later versions.                                                                                                                                                                                                                                               |
      +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | available_zone        | Yes                                   | Array of strings | AZ where the load balancer is located.                                                                                                                                                                                                                                                                                                                |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | You can obtain all supported AZs by `getting the AZ list <https://docs.otc.t-systems.com/api/elb/ListAvailabilityZones.html>`__.                                                                                                                                                                                                                      |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | This parameter is available only for dedicated load balancers.                                                                                                                                                                                                                                                                                        |
      +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | l4_flavor_name        | Yes                                   | String           | Flavor name of the layer-4 load balancer.                                                                                                                                                                                                                                                                                                             |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | You can obtain all supported types by `getting the flavor list <https://docs.otc.t-systems.com/api/elb/ListFlavors.html>`__.                                                                                                                                                                                                                          |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | This parameter is available only for dedicated load balancers.                                                                                                                                                                                                                                                                                        |
      +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | l7_flavor_name        | No                                    | String           | Flavor name of the layer-7 load balancer.                                                                                                                                                                                                                                                                                                             |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | You can obtain all supported types by `getting the flavor list <https://docs.otc.t-systems.com/api/elb/ListFlavors.html>`__.                                                                                                                                                                                                                          |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | This parameter is available only for dedicated load balancers. The value of this parameter must be the same as that of **l4_flavor_name**, that is, both are elastic specifications or fixed specifications.                                                                                                                                          |
      +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | elb_virsubnet_ids     | No                                    | Array of strings | Subnet where the backend server of the load balancer is located. If this parameter is left blank, the default cluster subnet is used. Load balancers occupy different number of subnet IP addresses based on their specifications. Do not use the subnet CIDR blocks of other resources (such as clusters and nodes) as the load balancer CIDR block. |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | This parameter is available only for dedicated load balancers.                                                                                                                                                                                                                                                                                        |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | Example:                                                                                                                                                                                                                                                                                                                                              |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | .. code-block::                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  |    "elb_virsubnet_ids": [                                                                                                                                                                                                                                                                                                                             |
      |                       |                                       |                  |       "14567f27-8ae4-42b8-ae47-9f847a4690dd"                                                                                                                                                                                                                                                                                                          |
      |                       |                                       |                  |     ]                                                                                                                                                                                                                                                                                                                                                 |
      +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | ipv6_vip_virsubnet_id | No                                    | String           | ID of the IPv6 subnet where the load balancer resides. IPv6 must be enabled for the corresponding subnet. This parameter is mandatory only when the dual-stack clusters are used.                                                                                                                                                                     |
      |                       |                                       |                  |                                                                                                                                                                                                                                                                                                                                                       |
      |                       |                                       |                  | This parameter is available only for dedicated load balancers.                                                                                                                                                                                                                                                                                        |
      +-----------------------+---------------------------------------+------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Create an ingress.

   .. code-block::

      kubectl create -f ingress-test.yaml

   If information similar to the following is displayed, the ingress has been created:

   .. code-block::

      ingress/ingress-test created

#. Check the created ingress.

   .. code-block::

      kubectl get ingress

   If information similar to the following is displayed, the ingress has been created:

   .. code-block::

      NAME          CLASS    HOSTS     ADDRESS          PORTS   AGE
      ingress-test  cce      *         121.**.**.**     80      10s

#. Enter **http://121.**.**.*\*:80** in the address box of the browser to access the workload (for example, :ref:`Nginx workload <cce_10_0047__section155246177178>`).

   **121.**.**.*\*** indicates the IP address of the unified load balancer.

.. _cce_10_0252__section32300431736:

Associating an Existing Load Balancer to an Ingress While Creating the Ingress
------------------------------------------------------------------------------

CCE allows you to connect to an existing load balancer when creating an ingress.

#. Use kubectl to access the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create a YAML file named **ingress-test.yaml**. The file name can be customized.

   .. code-block::

      vi ingress-test.yaml

   .. note::

      -  Starting from cluster v1.23, the ingress version is switched from **networking.k8s.io/v1beta1** to **networking.k8s.io/v1**. For details about the differences between v1 and v1beta1, see :ref:`Ingress API Version Upgrade in CCE Clusters v1.23 <cce_10_0252__section8926910153415>`.
      -  An existing dedicated load balancer must be of the application type (HTTP/HTTPS) and support private networks (with a private IP address).

   **If the cluster version is 1.23 or later, the YAML file configuration is as follows:**

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        name: ingress-test
        annotations:
          kubernetes.io/elb.id: <your_elb_id>  # Replace it with the ID of your existing load balancer.
          kubernetes.io/elb.ip: <your_elb_ip>  # Replace it with the IP of your existing load balancer.
          kubernetes.io/elb.class: performance  # Load balancer type
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
                    number: 8080             # Replace 8080 with the port number of your target Service.
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
              pathType: ImplementationSpecific
        ingressClassName: cce

   **If the cluster version is 1.21 or earlier, the YAML file configuration is as follows:**

   .. code-block::

      apiVersion: networking.k8s.io/v1beta1
      kind: Ingress
      metadata:
        name: ingress-test
        annotations:
          kubernetes.io/elb.id: <your_elb_id>  # Replace it with the ID of your existing load balancer.
          kubernetes.io/elb.ip: <your_elb_ip>  # Replace it with the IP of your existing load balancer.
          kubernetes.io/elb.class: performance  # Load balancer type
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

      +-------------------------+-----------------+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter               | Mandatory       | Type            | Description                                                                                                                                                                                             |
      +=========================+=================+=================+=========================================================================================================================================================================================================+
      | kubernetes.io/elb.id    | Yes             | String          | ID of a load balancer. The value can contain 1 to 100 characters.                                                                                                                                       |
      |                         |                 |                 |                                                                                                                                                                                                         |
      |                         |                 |                 | **How to obtain**:                                                                                                                                                                                      |
      |                         |                 |                 |                                                                                                                                                                                                         |
      |                         |                 |                 | On the management console, click **Service List**, and choose **Networking** > **Elastic Load Balance**. Click the name of the target load balancer. On the **Summary** tab page, find and copy the ID. |
      +-------------------------+-----------------+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.ip    | No              | String          | Service address of a load balancer. The value can be the public IP address of a public network load balancer or the private IP address of a private network load balancer.                              |
      +-------------------------+-----------------+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.class | Yes             | String          | Load balancer type.                                                                                                                                                                                     |
      |                         |                 |                 |                                                                                                                                                                                                         |
      |                         |                 |                 | -  **union**: shared load balancer                                                                                                                                                                      |
      |                         |                 |                 | -  **performance**: dedicated load balancer, which can be used only in clusters of v1.17 and later.                                                                                                     |
      |                         |                 |                 |                                                                                                                                                                                                         |
      |                         |                 |                 | .. note::                                                                                                                                                                                               |
      |                         |                 |                 |                                                                                                                                                                                                         |
      |                         |                 |                 |    If a LoadBalancer ingress accesses an existing dedicated load balancer, the dedicated load balancer must be of the application load balancing (HTTP/HTTPS) type.                                     |
      +-------------------------+-----------------+-----------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Create an ingress.

   .. code-block::

      kubectl create -f ingress-test.yaml

   If information similar to the following is displayed, the ingress has been created:

   .. code-block::

      ingress/ingress-test created

#. Check the created ingress.

   .. code-block::

      kubectl get ingress

   If information similar to the following is displayed, the ingress has been created:

   .. code-block::

      NAME          CLASS    HOSTS     ADDRESS          PORTS   AGE
      ingress-test  cce      *         121.**.**.**     80      10s

#. Enter **http://121.**.**.**:80** in the address box of the browser to access the workload (for example, :ref:`Nginx workload <cce_10_0047__section155246177178>`).

   **121.**.**.*\*** indicates the IP address of the unified load balancer.

.. |image1| image:: /_static/images/en-us_image_0000002101597577.png
