:original_name: cce_10_0390.html

.. _cce_10_0390:

Creating an Nginx Ingress on the Console
========================================

In Kubernetes, an ingress is a resource object that controls how Services within a cluster can be accessed from outside the cluster. You can use ingresses to configure different forwarding rules to access pods in a cluster. The following uses an :ref:`Nginx workload <cce_10_0047__section155246177178>` as an example to describe how to create an Nginx ingress on the console.

Prerequisites
-------------

-  An ingress provides network access for backend workloads. Ensure that a workload is available in a cluster. If no workload is available, deploy a workload by referring to :ref:`Creating a Deployment <cce_10_0047>`, :ref:`Creating a StatefulSet <cce_10_0048>`, or :ref:`Creating a DaemonSet <cce_10_0216>`.
-  A ClusterIP or NodePort Service has been configured for the workload. For details about how to configure the Service, see :ref:`ClusterIP <cce_10_0011>` or :ref:`NodePort <cce_10_0142>`.
-  To add Nginx Ingress, ensure that the NGINX Ingress Controller add-on has been installed in the cluster. For details, see :ref:`Installing the Add-on <cce_10_0034__section1152424015224>`.

Notes and Constraints
---------------------

-  **It is not recommended that you modify any configuration of a load balancer on the ELB console. Otherwise, the Service will be abnormal.** If you have modified the configuration, uninstall the nginx-ingress add-on and reinstall it.
-  The URL registered in an ingress forwarding policy must be the same as the URL used to access the backend Service. Otherwise, a 404 error will be returned.
-  The selected or created load balancer must be in the same VPC as the current cluster, and it must match the load balancer type (private or public network).
-  The load balancer has at least two listeners, and ports 80 and 443 are not occupied by listeners.
-  In CCE standard clusters, when the NGINX Ingress Controller is bound to a dedicated load balancer, the node hosting the **cceaddon-nginx-ingress-controller** pod, and any other containers on that same node, cannot use the load balancer's private IP to access the ingress.

Creating an Nginx Ingress
-------------------------

This section uses an Nginx workload as an example to describe how to create an Nginx ingress.

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. Choose **Services and Ingresses** in the navigation pane, click the **Ingresses** tab, and click **Create Ingress** in the upper right corner.

#. Configure ingress parameters.

   -  **Name**: Enter a name for the ingress, for example, **nginx-ingress-demo**.
   -  **Namespace**: Select the namespace to which the ingress is to be added.
   -  **nginx-ingress**: This option is displayed only after the :ref:`NGINX Ingress Controller <cce_10_0034>` add-on is installed in the cluster.

      -  **Ingress Class**: Select the name of the Nginx Ingress controller installed in the cluster. You can :ref:`deploy multiple Nginx Ingress controllers <cce_10_0034__section1057051834311>` and customize their names as needed.
      -  **Frontend Protocol**: The options are **HTTP** and **HTTPS**. By default, NGINX Ingress Controller listens on ports 80 for HTTP and 443 for HTTPS. To use HTTPS, configure a certificate.
      -  **Certificate Source**: the source of a certificate for encrypting and authenticating HTTPS data transmission.

         -  If you select a TLS key, you must create a key certificate of the IngressTLS or kubernetes.io/tls type beforehand. For details, see :ref:`Creating a Secret <cce_10_0153>`.
         -  If you select **Default certificate**, NGINX Ingress Controller will use its default certificate for encryption and authentication. You can override this default while installing :ref:`NGINX Ingress Controller <cce_10_0034>`. Otherwise, the controller's built-in certificate will be used.

      -  **SNI**: stands for Server Name Indication (SNI), which is an extended protocol of TLS. SNI enables several TLS domain names to share one IP:port pair while using different security certificates for each domain name. With SNI enabled, the client can send the requested domain name during the TLS handshake. The load balancer uses the domain name from the TLS request to find the matching certificate. If found, it returns that certificate. Otherwise, it returns the default server certificate.

   -  **Forwarding Policy**: When the access address of a request matches the forwarding policy (a forwarding policy consists of a domain name and URL), the request is forwarded to the corresponding target Service for processing. Click **Add Forwarding Policies** to add multiple forwarding policies.

      -  **Domain Name**: actual domain name. Ensure that the entered domain name has been registered and archived. After the ingress is created, bind the domain name to the IP address of the automatically created load balancer (IP address of the ingress access address). If a domain name rule is configured, the domain name must always be used for access.
      -  **Path Matching Rule**:

         -  **Default**: If **Default** is selected, **Prefix match** will be used.
         -  **Prefix match**: If the URL is set to **/healthz**, the URL that meets the prefix can be accessed, for example, **/healthz/v1** and **/healthz/v2**.
         -  **Exact match**: The URL can be accessed only when it is fully matched. For example, if the URL is set to **/healthz**, only **/healthz** can be accessed.

      -  **Path**: access path, for example, **/healthz**

         .. note::

            -  The access path matching rule of Nginx Ingress is based on the path prefix separated by the slash (/) and is case-sensitive. If the subpath separated by a slash (/) matches the prefix, the access is normal. However, if the prefix is only a part of the character string in the subpath, the access is not matched. For example, if the URL is set to /healthz, /healthz/v1 is matched, but /healthzv1 is not matched.

            -  The access path added here must exist in the backend application. Otherwise, the forwarding fails.

               For example, the default access URL of the Nginx application is **/usr/share/nginx/html**. When adding **/test** to the ingress forwarding policy, ensure the access URL of your Nginx application contains **/usr/share/nginx/html/test**. Otherwise, error 404 will be returned.

      -  **Destination Service**: Select an existing Service. Only Services that meet the requirements are automatically displayed in the Service list.
      -  **Destination Service Port**: Select the access port of the destination Service.
      -  **Operation**: Click **Delete** to delete the configuration.

   -  **Annotation**: The value is in the format of key-value pairs. You can use `annotations <https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/>`__ to obtain the configurations supported by Nginx ingresses.

#. Click **OK**.

   After the ingress is created, it is displayed in the ingress list.
