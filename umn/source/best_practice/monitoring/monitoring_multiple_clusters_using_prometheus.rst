:original_name: cce_bestpractice_10009.html

.. _cce_bestpractice_10009:

Monitoring Multiple Clusters Using Prometheus
=============================================

Application Scenarios
---------------------

In most cases, users have more than one cluster, for example, production, testing, and development clusters. If Prometheus is deployed in every cluster to monitor service metrics, the maintenance and resource costs increase significantly, and it becomes inconvenient to aggregate the data for unified viewing. In such scenarios, you can deploy a single Prometheus instance and connect it to multiple clusters to collect their metric data.

Solution Architecture
---------------------

Connect multiple clusters to the same Prometheus monitoring system, as shown in the figure below. This reduces maintenance and resource costs and facilitates monitoring information aggregation.

|image1|

Prerequisites
-------------

-  The needed clusters are available.
-  Prometheus has been properly connected to these clusters.
-  Prometheus has been installed on a Linux host using a binary file. For details, see `Installation <https://prometheus.io/docs/prometheus/latest/installation/>`__.

Procedure
---------

#. Obtain the **bearer_token** information of these clusters.

   a. Create the RBAC permissions in a cluster.

      Log in to a node of the cluster and create a **prometheus_rbac.yaml** file.

      .. code-block::

         apiVersion: v1
         kind: ServiceAccount
         metadata:
           name: prometheus-test
           namespace: kube-system

         ---
         apiVersion: rbac.authorization.k8s.io/v1
         kind: ClusterRole
         metadata:
           name: prometheus-test
         rules:
         - apiGroups:
           - ""
           resources:
           - nodes
           - services
           - endpoints
           - pods
           - nodes/proxy
           verbs:
           - get
           - list
           - watch
         - apiGroups:
           - "extensions"
           resources:
             - ingresses
           verbs:
           - get
           - list
           - watch
         - apiGroups:
           - ""
           resources:
           - configmaps
           - nodes/metrics
           verbs:
           - get
         - nonResourceURLs:
           - /metrics
           verbs:
           - get
         ---
         apiVersion: rbac.authorization.k8s.io/v1
         kind: ClusterRoleBinding
         metadata:
           name: prometheus-test
         roleRef:
           apiGroup: rbac.authorization.k8s.io
           kind: ClusterRole
           name: prometheus-test
         subjects:
         - kind: ServiceAccount
           name: prometheus-test
           namespace: kube-system

      Create the RBAC permissions.

      **kubectl apply -f prometheus_rbac.yaml**

   b. Obtain the **bearer_token** information of the cluster.

      .. note::

         -  In clusters earlier than v1.21, tokens are obtained by mounting the secret of a service account to a pod. Such tokens are permanent. However, this approach is not recommended in clusters of v1.21 or later. Starting from v1.25, Kubernetes no longer automatically creates secrets for service accounts as part of its community iteration policy.

            Instead, in clusters of v1.21 and later, the recommended approach is to use the `TokenRequest <https://kubernetes.io/docs/reference/kubernetes-api/authentication-resources/token-request-v1/>`__ API to `obtain tokens <https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/#bound-service-account-token-volume>`__ and mount them via a projected volume to pods. These tokens remain valid only for a fixed period and become invalid once the pods are deleted.

         -  If you need a token that never expires, you can `manually manage secrets for service accounts <https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/#manual-secret-management-for-serviceaccounts>`__. Although a permanent service account token can be created manually, you are advised to use a short-lived token by calling the `TokenRequest <https://kubernetes.io/docs/reference/kubernetes-api/authentication-resources/token-request-v1/>`__ API for better security.

      Obtain the **serviceaccount** information.

      **kubectl describe sa prometheus-test -n kube-system**

      |image2|

      **kubectl describe secret prometheus-test-token-hdhkg -n kube-system**

      |image3|

      Record the token value, which is the **bearer_token** information to be collected.

#. Configure **bearer_token**.

   Log in to the host where Prometheus is located, go to the Prometheus installation directory, and save the cluster tokens in a file.

   |image4|

#. Configure a Prometheus monitoring job.

   The example job monitors container metrics. To monitor other metrics, you can add jobs and write capture rules.

   .. code-block::

        - job_name: k8s_cAdvisor
          scheme: https
          bearer_token_file: k8s_token # Token file in the previous step.
          tls_config:
            insecure_skip_verify: true
          kubernetes_sd_configs:  # Kubernetes automatic discovery
          - role: node    # Automatic discovery of the node type
            bearer_token_file: k8s_token # Token file in the previous step
            api_server: https://192.168.0.153:5443  # API server address of the Kubernetes cluster
            tls_config:
              insecure_skip_verify: true   # Skip the server authentication.
          relabel_configs:  ## Modify the existing label of the cluster before capturing metrics.
          - target_label: __address__
            replacement: 192.168.0.153:5443
            action: replace
            ## Convert metrics_path to /api/v1/nodes/${1}/proxy/metrics/cadvisor.
            # Obtain data from kubelet using the API server proxy.
          - source_labels: [__meta_kubernetes_node_name]   # The source label to be processed
            regex: (.+)    # Matched value of the source label. (.+) indicates that any value of the source label can be matched.
            target_label: __metrics_path__     # The label to be replaced
            replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor  # The new label, that is, the value of __metrics_path__. ${1} indicates the value that matches the regular expression, that is, node name.
          - target_label: cluster
            replacement: xxxxx   ## (Optional) Enter the cluster information.

      ### The following job monitors another cluster.
        - job_name: k8s02_cAdvisor
          scheme: https
          bearer_token_file: k8s02_token # Token file in the previous step
          tls_config:
            insecure_skip_verify: true
          kubernetes_sd_configs:
          - role: node
            bearer_token_file: k8s02_token # Token file in the previous step
            api_server: https://192.168.0.147:5443  # API server address of the Kubernetes cluster
            tls_config:
              insecure_skip_verify: true   # Skip the server authentication.
          relabel_configs:  ## Modify the existing label of the cluster before capturing metrics.
          - target_label: __address__
            replacement: 192.168.0.147:5443
            action: replace

          - source_labels: [__meta_kubernetes_node_name]
            regex: (.+)
            target_label: __metrics_path__
            replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor

          - target_label: cluster
            replacement: xxxx    ## (Optional) Enter the cluster information.

#. Enable Prometheus.

   After the configuration, enable Prometheus.

   **./prometheus --config.file=prometheus.yml**

#. Log in to Prometheus and view the monitoring information.

   |image5|

   |image6|

.. |image1| image:: /_static/images/en-us_image_0000002631531292.png
.. |image2| image:: /_static/images/en-us_image_0000002631691194.png
.. |image3| image:: /_static/images/en-us_image_0000002631531296.png
.. |image4| image:: /_static/images/en-us_image_0000002631691198.png
.. |image5| image:: /_static/images/en-us_image_0000002631691190.png
.. |image6| image:: /_static/images/en-us_image_0000002631531300.png
