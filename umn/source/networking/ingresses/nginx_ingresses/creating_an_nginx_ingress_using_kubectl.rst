:original_name: cce_10_0364.html

.. _cce_10_0364:

Creating an Nginx Ingress Using kubectl
=======================================

This section uses an :ref:`Nginx workload <cce_10_0047__section155246177178>` as an example to describe how to create an Nginx ingress using kubectl.

.. _cce_10_0364__section084115985013:

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

-  An ingress provides network access for backend workloads. Ensure that a workload is available in a cluster. If no workload is available, deploy a workload by referring to :ref:`Creating a Deployment <cce_10_0047>`, :ref:`Creating a StatefulSet <cce_10_0048>`, or :ref:`Creating a DaemonSet <cce_10_0216>`.
-  A ClusterIP or NodePort Service has been configured for the workload. For details about how to configure the Service, see :ref:`ClusterIP <cce_10_0011>` or :ref:`NodePort <cce_10_0142>`.
-  To add Nginx Ingress, ensure that the NGINX Ingress Controller add-on has been installed in the cluster. For details, see :ref:`Installing the Add-on <cce_10_0034__section1152424015224>`.

Notes and Constraints
---------------------

-  **It is not recommended modifying any configuration of a load balancer on the ELB console. Otherwise, the Service will be abnormal.** If you have modified the configuration, uninstall the nginx-ingress add-on and reinstall it.
-  The URL registered in an ingress forwarding policy must be the same as the URL used to access the backend Service. Otherwise, a 404 error will be returned.
-  The selected or created load balancer must be in the same VPC as the current cluster, and it must match the load balancer type (private or public network).
-  The load balancer has at least two listeners, and ports 80 and 443 are not occupied by listeners.
-  When NGINX Ingress Controller accesses a dedicated load balancer and the service affinity is set to the node level, the load balancer's IP address may fail to be accessed within the cluster. For details, see :ref:`Why a Service Fails to Be Accessed from Within the Cluster <cce_10_0249__section52631714117>`.

Creating an Nginx Ingress
-------------------------

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Create a YAML file named **ingress-test.yaml**. The file name can be customized.

   .. code-block::

      vi ingress-test.yaml

   .. note::

      Starting from cluster v1.23, the ingress version is switched from **networking.k8s.io/v1beta1** to **networking.k8s.io/v1**. For details about the differences between v1 and v1beta1, see :ref:`Ingress API Version Upgrade in CCE Clusters v1.23 <cce_10_0364__section084115985013>`.

   The following uses HTTP as an example to describe how to configure the YAML file:

   **For clusters of v1.23 or later:**

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        name: ingress-test
      spec:
        rules:
          - host: ''
            http:
              paths:
                - path: /
                  backend:
                    service:
                      name: <your_service_name>  # Replace it with the name of your target Service.
                      port:
                        number: <your_service_port>  # Replace it with the port number of your target Service.
                  property:
                    ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
                  pathType: ImplementationSpecific
        ingressClassName: nginx   # Nginx ingresses are used. If multiple NGINX Ingress Controllers are installed in the cluster, replace nginx with the custom names of the controllers <cce_10_0034__li0953175016455> associated with the ingresses.

   **For clusters of v1.21 or earlier:**

   .. code-block::

      apiVersion: networking.k8s.io/v1beta1
      kind: Ingress
      metadata:
        name: ingress-test
        namespace: default
        annotations:
          kubernetes.io/ingress.class: nginx   # Nginx Ingress is used.
      spec:
        rules:
          - host: ''
            http:
              paths:
                - path: '/'
                  backend:
                    serviceName: <your_service_name>  # Replace it with the name of your target Service.
                    servicePort: <your_service_port>  # Replace it with the port number of your target Service.

   .. table:: **Table 1** Key parameters

      +-------------------------------------------+---------------------------------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                                 | Mandatory                                   | Type            | Description                                                                                                                                                                                                                                                                                                                                                                                                                      |
      +===========================================+=============================================+=================+==================================================================================================================================================================================================================================================================================================================================================================================================================================+
      | kubernetes.io/ingress.class               | Yes (only for clusters of v1.21 or earlier) | String          | **nginx**: indicates that Nginx Ingress is used. This option is available only after the NGINX Ingress Controller add-on is installed.                                                                                                                                                                                                                                                                                           |
      |                                           |                                             |                 |                                                                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                           |                                             |                 | This parameter is mandatory when you create an Nginx ingress. If it is not specified, a LoadBalancer ingress will be created by default.                                                                                                                                                                                                                                                                                         |
      +-------------------------------------------+---------------------------------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | ingressClassName                          | Yes                                         | String          | **nginx**: indicates that Nginx Ingress is used. This option is available only after the NGINX Ingress Controller add-on is installed.                                                                                                                                                                                                                                                                                           |
      |                                           |                                             |                 |                                                                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                           | (only for clusters of v1.23 or later)       |                 | Multiple NGINX Ingress Controller add-ons can be installed in a single cluster if the add-on version is 2.5.4 or later. In this case, the value of this parameter must be the :ref:`controller name <cce_10_0034__li0953175016455>` customized during controller installation, indicating that the ingress is managed by that specific controller.                                                                               |
      |                                           |                                             |                 |                                                                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                           |                                             |                 | This parameter is mandatory when you create an Nginx ingress. If it is not specified, a LoadBalancer ingress will be created by default.                                                                                                                                                                                                                                                                                         |
      +-------------------------------------------+---------------------------------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | host                                      | No                                          | String          | Domain name for accessing the Service. By default, this parameter is left blank, and the domain name needs to be fully matched. Ensure that the domain name has been registered and licensed. Once a forwarding policy is configured with a domain name specified, you must use the domain name for access.                                                                                                                      |
      +-------------------------------------------+---------------------------------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | path                                      | Yes                                         | String          | User-defined route path. All external access requests must match **host** and **path**.                                                                                                                                                                                                                                                                                                                                          |
      |                                           |                                             |                 |                                                                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                           |                                             |                 | .. note::                                                                                                                                                                                                                                                                                                                                                                                                                        |
      |                                           |                                             |                 |                                                                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                           |                                             |                 |    -  The access path matching rule of Nginx Ingress is based on the path prefix separated by the slash (/) and is case-sensitive. If the subpath separated by a slash (/) matches the prefix, the access is normal. However, if the prefix is only a part of the character string in the subpath, the access is not matched. For example, if the URL is set to /healthz, /healthz/v1 is matched, but /healthzv1 is not matched. |
      |                                           |                                             |                 |                                                                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                           |                                             |                 |    -  The access path added here must exist in the backend application. Otherwise, the forwarding fails.                                                                                                                                                                                                                                                                                                                         |
      |                                           |                                             |                 |                                                                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                           |                                             |                 |       For example, the default access URL of the Nginx application is **/usr/share/nginx/html**. When adding **/test** to the ingress forwarding policy, ensure the access URL of your Nginx application contains **/usr/share/nginx/html/test**. Otherwise, error 404 will be returned.                                                                                                                                         |
      +-------------------------------------------+---------------------------------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | ingress.beta.kubernetes.io/url-match-mode | No                                          | String          | Route matching policy.                                                                                                                                                                                                                                                                                                                                                                                                           |
      |                                           |                                             |                 |                                                                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                           |                                             |                 | Default: **STARTS_WITH** (prefix match)                                                                                                                                                                                                                                                                                                                                                                                          |
      |                                           |                                             |                 |                                                                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                           |                                             |                 | Options:                                                                                                                                                                                                                                                                                                                                                                                                                         |
      |                                           |                                             |                 |                                                                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                           |                                             |                 | -  **EQUAL_TO**: exact match                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                           |                                             |                 | -  **STARTS_WITH**: prefix match                                                                                                                                                                                                                                                                                                                                                                                                 |
      +-------------------------------------------+---------------------------------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | pathType                                  | Yes                                         | String          | Path type. This field is supported only by clusters of v1.23 or later.                                                                                                                                                                                                                                                                                                                                                           |
      |                                           |                                             |                 |                                                                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                           |                                             |                 | -  **ImplementationSpecific**: The matching method depends on Ingress Controller. The matching method defined by **ingress.beta.kubernetes.io/url-match-mode** is used in CCE.                                                                                                                                                                                                                                                   |
      |                                           |                                             |                 | -  **Exact**: exact matching of the URL, which is case-sensitive.                                                                                                                                                                                                                                                                                                                                                                |
      |                                           |                                             |                 | -  **Prefix**: prefix matching, which is case-sensitive. With this method, the URL path is separated into multiple elements by slashes (/) and the elements are matched one by one. If each element in the URL matches the path, the subpaths of the URL can be routed normally.                                                                                                                                                 |
      |                                           |                                             |                 |                                                                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                           |                                             |                 |    .. note::                                                                                                                                                                                                                                                                                                                                                                                                                     |
      |                                           |                                             |                 |                                                                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                           |                                             |                 |       -  During prefix matching, each element must be exactly matched. If the last element of the URL is the substring of the last element in the request path, no matching is performed. For example, **/foo/bar** matches **/foo/bar/baz** but does not match **/foo/barbaz**.                                                                                                                                                 |
      |                                           |                                             |                 |       -  When elements are separated by slashes (/), if the URL or request path ends with a slash (/), the slash (/) at the end is ignored. For example, **/foo/bar** matches **/foo/bar/**.                                                                                                                                                                                                                                     |
      |                                           |                                             |                 |                                                                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                           |                                             |                 | See `examples <https://kubernetes.io/docs/concepts/services-networking/ingress/>`__ of ingress path matching.                                                                                                                                                                                                                                                                                                                    |
      +-------------------------------------------+---------------------------------------------+-----------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

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

      NAME          CLASS   HOSTS     ADDRESS          PORTS   AGE
      ingress-test  nginx   *         121.**.**.**     80      10s

#. Enter **http://121.**.**.*\*:80** in the address bar of the browser to access the workload (for example, :ref:`Nginx workload <cce_10_0047__section155246177178>`).

   **121.**.**.*\*** indicates the IP address of the unified load balancer.

.. |image1| image:: /_static/images/en-us_image_0000002516079007.png
