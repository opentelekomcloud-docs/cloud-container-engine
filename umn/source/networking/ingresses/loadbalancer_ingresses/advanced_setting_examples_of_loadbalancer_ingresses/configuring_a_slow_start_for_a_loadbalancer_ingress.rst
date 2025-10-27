:original_name: cce_10_0735.html

.. _cce_10_0735:

Configuring a Slow Start for a LoadBalancer Ingress
===================================================

With slow start configured, a load balancer linearly increases the proportion of requests to backend server pods. When the slow start duration elapses, the load balancer sends full share of requests to backend server pods and exits the slow start mode. Slow start gives applications time to warm up and respond to requests with optimal performance.

.. note::

   After a slow start is configured, if you delete the target annotation from the YAML file, slow start will not be enabled.

Prerequisites
-------------

-  A CCE standard or Turbo cluster is available, and the cluster version is v1.23 or later.
-  An available workload has been deployed in the cluster for external access. If no workload is available, deploy a workload by referring to :ref:`Creating a Deployment <cce_10_0047>`, :ref:`Creating a StatefulSet <cce_10_0048>`, or :ref:`Creating a DaemonSet <cce_10_0216>`.
-  A Service for external access has been configured for the workload. :ref:`Services Supported by LoadBalancer Ingresses <cce_10_0094__section3565202819276>` lists the Service types supported by LoadBalancer ingresses.

Notes and Constraints
---------------------

-  Slow start is only available for HTTP, gRPC, or HTTPS backend server groups (pods) of dedicated load balancers.
-  Slow start takes effect only when the weighted round robin algorithm is used.
-  Slow start takes effect only for new backend server pods.
-  After the slow start duration elapses, backend servers will not enter the slow start mode again.
-  Slow start takes effect when health check is enabled and backend server pods are running properly.
-  If health check is disabled, slow start takes effect immediately.
-  With slow start configured for an ingress, all forwarding policies of the ingress take effect.

Procedure
---------

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Create a YAML file named **ingress-test.yaml**. The file name can be customized.

   .. code-block::

      vi ingress-test.yaml

   An example YAML file of an ingress associated with an existing load balancer is as follows:

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        name: ingress-test
        namespace: default
        annotations:
          kubernetes.io/elb.port: '80'
          kubernetes.io/elb.id: <your_elb_id>  # Replace it with the ID of your existing load balancer.
          kubernetes.io/elb.class: performance
          kubernetes.io/elb.slowstart: '30' #Configure a slow start.
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
                        number: 80             # Replace 80 with the port number of your target Service.
                  property:
                    ingress.beta.kubernetes.io/url-match-mode: STARTS_WITH
                  pathType: ImplementationSpecific
        ingressClassName: cce

   .. table:: **Table 1** Parameters for configuring a slow start

      +-----------------------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                   | Mandatory       | Type            | Description                                                                                                                                                                                                                                                                                                  |
      +=============================+=================+=================+==============================================================================================================================================================================================================================================================================================================+
      | kubernetes.io/elb.slowstart | No              | String          | Specifies whether to enable slow start. After you enable it, the load balancer linearly increases the proportion of requests to backend server pods in this mode. When the slow start duration elapses, the load balancer sends full share of requests to backend server pods and exits the slow start mode. |
      |                             |                 |                 |                                                                                                                                                                                                                                                                                                              |
      |                             |                 |                 | **Clusters later than v1.23 support this field.**                                                                                                                                                                                                                                                            |
      |                             |                 |                 |                                                                                                                                                                                                                                                                                                              |
      |                             |                 |                 | The slow start duration ranges from **30** to **1200**.                                                                                                                                                                                                                                                      |
      |                             |                 |                 |                                                                                                                                                                                                                                                                                                              |
      |                             |                 |                 | Duration of slow start, in seconds.                                                                                                                                                                                                                                                                          |
      |                             |                 |                 |                                                                                                                                                                                                                                                                                                              |
      |                             |                 |                 | -  Grayscale release applies only to dedicated load balancers.                                                                                                                                                                                                                                               |
      |                             |                 |                 | -  This parameter is valid only when the allocation policy of the target Service is weighted round robin (WRR) and sticky session is disabled.                                                                                                                                                               |
      +-----------------------------+-----------------+-----------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

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
