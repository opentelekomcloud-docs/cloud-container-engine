:original_name: cce_10_0937.html

.. _cce_10_0937:

Configuring a Range of Listening Ports for a LoadBalancer Ingress
=================================================================

Ingress allows you to customize listening ports. You can configure both HTTP and HTTPS listeners for a Service. For example, a Service can make available both HTTP port 80 and HTTPS port 443 for external access.

Prerequisites
-------------

-  A CCE standard or Turbo cluster is available, and the cluster version meets the following requirements:

   -  v1.23: v1.23.14-r0 or later
   -  v1.25: v1.25.9-r0 or later
   -  v1.27: v1.27.6-r0 or later
   -  v1.28: v1.28.4-r0 or later
   -  Other clusters of later versions

-  An available workload has been deployed in the cluster for external access. If no workload is available, deploy a workload by referring to :ref:`Creating a Deployment <cce_10_0047>`, :ref:`Creating a StatefulSet <cce_10_0048>`, or :ref:`Creating a DaemonSet <cce_10_0216>`.
-  A Service for external access has been configured for the workload. :ref:`Services Supported by LoadBalancer Ingresses <cce_10_0094__section3565202819276>` lists the Service types supported by LoadBalancer ingresses.

Using kubectl
-------------

#. Use kubectl to access the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create a YAML file named **ingress-test.yaml**. The file name can be customized.

   .. code-block::

      vi ingress-test.yaml

   The following shows an example configuration using an existing load balancer:

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        annotations:
          kubernetes.io/elb.id: 2c623150-17bf-45f1-ae6f-384b036f547e     # ID of an existing load balancer
          kubernetes.io/elb.class: performance    # Load balancer type
          kubernetes.io/elb.listen-ports: '[{"HTTP": 80},{"HTTPS": 443}]'    # Multi-listener configuration
          kubernetes.io/elb.tls-certificate-ids: 6cfb43c9de1a41a18478b868e34b0a82,6cfb43c9de1a41a18478b868e34b0a82   # HTTPS certificate configuration
        name: ingress-test
        namespace: default
      spec:
        ingressClassName: cce
        rules:
        - host: example.com
          http:
            paths:
            - backend:
                service:
                  name: test
                  port:
                    number: 8888
              path: /
              pathType: ImplementationSpecific
              property:
                ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH

   .. table:: **Table 1** Annotations for custom listening ports

      +--------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                      | Type                  | Description                                                                                                                                                                                                                                                                                                                                                                                                                                       |
      +================================+=======================+===================================================================================================================================================================================================================================================================================================================================================================================================================================================+
      | kubernetes.io/elb.listen-ports | String                | Configure multiple listening ports for an ingress. The port number ranges from 1 to 65535.                                                                                                                                                                                                                                                                                                                                                        |
      |                                |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
      |                                |                       | The following is an example for JSON characters:                                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
      |                                |                       | .. code-block::                                                                                                                                                                                                                                                                                                                                                                                                                                   |
      |                                |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
      |                                |                       |    kubernetes.io/elb.listen-ports: '[{"HTTP":80},{"HTTPS":443}]'                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                |                       |                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
      |                                |                       | -  Only the listening ports that comply with both HTTP and HTTPS are allowed.                                                                                                                                                                                                                                                                                                                                                                     |
      |                                |                       | -  This function is available only for newly created ingresses in clusters of a version earlier than v1.23.18-r10, v1.25.16-r0, v1.27.16-r0, v1.28.13-r0, v1.29.8-r0, or v1.30.4-r0. Additionally, after you configure multiple listening ports, the annotations cannot be modified or deleted. In clusters of v1.23.18-r10, v1.25.16-r0, v1.27.16-r0, v1.28.13-r0, v1.29.8-r0, v1.30.4-r0, or later, the annotations can be modified or deleted. |
      |                                |                       | -  If both **kubernetes.io/elb.listen-ports** and **kubernetes.io/elb.port** are configured, **kubernetes.io/elb.listen-ports** takes a higher priority.                                                                                                                                                                                                                                                                                          |
      |                                |                       | -  Ingress configuration items such as the blocklist, trustlist, and timeout concurrently take effect on multiple listening ports. When HTTP/2 is enabled for an ingress, HTTP/2 takes effect only on the HTTPS port.                                                                                                                                                                                                                             |
      +--------------------------------+-----------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

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

      NAME          CLASS    HOSTS           ADDRESS          PORTS   AGE
      ingress-test  cce      example.com     121.**.**.**     80,443  10s
