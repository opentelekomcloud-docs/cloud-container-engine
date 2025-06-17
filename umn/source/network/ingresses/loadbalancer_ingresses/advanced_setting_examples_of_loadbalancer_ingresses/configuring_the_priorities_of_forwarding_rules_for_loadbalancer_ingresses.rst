:original_name: cce_10_0939.html

.. _cce_10_0939:

Configuring the Priorities of Forwarding Rules for LoadBalancer Ingresses
=========================================================================

When ingresses use the same load balancer listener, forwarding rules can be prioritized based on the following rules:

-  Forwarding rules of different ingresses: The rules are sorted based on the priorities (ranging from 1 to 1000) of the **kubernetes.io/elb.ingress-order** annotation. A smaller value indicates a higher priority.
-  Forwarding rules of an ingress: If the **kubernetes.io/elb.rule-priority-enabled** annotation is set to **true**, the forwarding rules are sorted based on the sequence in which they are added during ingress creation. A forwarding rule added earlier indicates a higher priority. If the **kubernetes.io/elb.rule-priority-enabled** annotation is not configured, the default sorting of the forwarding rules on the load balancer will be used.

If the preceding annotations are not configured, the default sorting of the forwarding rules on the load balancer will be used, regardless of whether the forwarding rules are of the same ingress or different ingresses under the same load balancer listener.

Prerequisites
-------------

-  A CCE standard or Turbo cluster is available, and the cluster version meets the following requirements:

   -  v1.23: v1.23.15-r0 or later
   -  v1.25: v1.25.10-r0 or later
   -  v1.27: v1.27.7-r0 or later
   -  v1.28: v1.28.5-r0 or later
   -  v1.29: v1.29.1-r10 or later
   -  Other clusters of later versions

-  An available workload has been deployed in the cluster for external access. If no workload is available, deploy a workload by referring to :ref:`Creating a Deployment <cce_10_0047>`, :ref:`Creating a StatefulSet <cce_10_0048>`, or :ref:`Creating a DaemonSet <cce_10_0216>`.
-  A Service for external access has been configured for the workload. :ref:`Services Supported by LoadBalancer Ingresses <cce_10_0094__section3565202819276>` lists the Service types supported by LoadBalancer ingresses.

Notes and Constraints
---------------------

-  This feature is only available when dedicated load balancers are used.

Using kubectl
-------------

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Create a YAML file named **ingress-test.yaml**. The file name can be customized.

   .. code-block::

      vi ingress-test.yaml

   The following shows an example configuration using an existing load balancer:

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        name: test
        namespace: default
        annotations:
          kubernetes.io/elb.port: '88'
          kubernetes.io/elb.id: 2c623150-17bf-45f1-ae6f-384b036f547e    # ID of an existing load balancer
          kubernetes.io/elb.class: performance    # Load balancer type
          kubernetes.io/elb.ingress-order: '1'    # Priority of a forwarding rule of different ingresses
          kubernetes.io/elb.rule-priority-enabled: 'true'   # The forwarding rules of an ingress are sorted based on the forwarding rule sequence in paths.
      spec:
        rules:
          - host: ''
            http:
              paths:
                - path: /test1
                  backend:
                    service:
                      name: test1
                      port:
                        number: 8081
                  property:
                    ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
                  pathType: ImplementationSpecific
                - path: /test2
                  backend:
                    service:
                      name: test2
                      port:
                        number: 8081
                  property:
                    ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
                  pathType: ImplementationSpecific
        ingressClassName: cce

   .. table:: **Table 1** Annotations for configuring the priorities of forwarding rules

      +-----------------------------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                               | Type                  | Description                                                                                                                                                                                                                                                                                  |
      +=========================================+=======================+==============================================================================================================================================================================================================================================================================================+
      | kubernetes.io/elb.ingress-order         | String                | Specifies the sequence of forwarding rules of different ingresses. The value ranges from 1 to 1000. A smaller value indicates a higher priority. The priority of a forwarding rule must be unique under the same load balancer listener.                                                     |
      |                                         |                       |                                                                                                                                                                                                                                                                                              |
      |                                         |                       | This parameter is available only for dedicated load balancers.                                                                                                                                                                                                                               |
      |                                         |                       |                                                                                                                                                                                                                                                                                              |
      |                                         |                       | .. note::                                                                                                                                                                                                                                                                                    |
      |                                         |                       |                                                                                                                                                                                                                                                                                              |
      |                                         |                       |    When this annotation is configured, the **kubernetes.io/elb.rule-priority-enabled** annotation is enabled by default. The forwarding rules of each ingress will be sorted.                                                                                                                |
      +-----------------------------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.rule-priority-enabled | String                | This parameter can only be set to **true**, indicating to sort the forwarding rules of an ingress. The priorities of the forwarding rules are determined based on the sequence in which they are added during ingress creation. A forwarding rule added earlier indicates a higher priority. |
      |                                         |                       |                                                                                                                                                                                                                                                                                              |
      |                                         |                       | If this parameter is not configured, the default sorting of the forwarding rules on the load balancer will be used. After this parameter is enabled, it cannot be disabled.                                                                                                                  |
      |                                         |                       |                                                                                                                                                                                                                                                                                              |
      |                                         |                       | This parameter is available only for dedicated load balancers.                                                                                                                                                                                                                               |
      +-----------------------------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

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
      ingress-test  cce      *         121.**.**.**     88      10s
