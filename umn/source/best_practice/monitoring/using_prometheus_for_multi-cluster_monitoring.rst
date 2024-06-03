:original_name: cce_bestpractice_10009.html

.. _cce_bestpractice_10009:

Using Prometheus for Multi-cluster Monitoring
=============================================

Application Scenarios
---------------------

Generally, a user has different clusters for different purposes, such as production, testing, and development. To monitor, collect, and view metrics of these clusters, you can deploy a set of Prometheus.

Solution Architecture
---------------------

Multiple clusters are connected to the same Prometheus monitoring system, as shown in the following figure. This reduces maintenance and resource costs and facilitates monitoring information aggregation.

|image1|

Prerequisites
-------------

-  The target cluster has been created.
-  Prometheus has been properly connected to the target cluster.
-  Prometheus has been installed on a Linux host using a binary file. For details, see `Installation <https://prometheus.io/docs/prometheus/latest/installation/>`__.

Procedure
---------

#. Obtain the **bearer_token** information of the target cluster.

   a. Create the RBAC permission in the target cluster.

      Log in to the background node of the target cluster and create the **prometheus_rbac.yaml** file.

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

      Run the following command to create the RBAC permission:

      **kubectl apply -f prometheus_rbac.yaml**

   b. Obtain the **bearer_token** information of the target cluster.

      .. note::

         -  In clusters earlier than v1.21, a token is obtained by mounting the secret of the service account to a pod. Tokens obtained this way are permanent. This approach is no longer recommended starting from version 1.21. Service accounts will stop auto creating secrets in clusters from version 1.25.

            In clusters of version 1.21 or later, you can use the `TokenRequest <https://kubernetes.io/docs/reference/kubernetes-api/authentication-resources/token-request-v1/>`__ API to `obtain the token <https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/#bound-service-account-token-volume>`__ and use the projected volume to mount the token to the pod. Such tokens are valid for a fixed period. When the mounting pod is deleted, the token automatically becomes invalid.

         -  If you need a token that never expires, you can also `manually manage secrets for service accounts <https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/#manual-secret-management-for-serviceaccounts>`__. Although a permanent service account token can be manually created, you are advised to use a short-lived token by calling the `TokenRequest <https://kubernetes.io/docs/reference/kubernetes-api/authentication-resources/token-request-v1/>`__ API for higher security.

      Obtain the **serviceaccount** information.

      **kubectl describe sa prometheus-test -n kube-system**

      |image2|

      **kubectl describe secret prometheus-test-token-hdhkg -n kube-system**

      |image3|

      Record the token value, which is the **bearer_token** information to be collected.

#. Configure **bearer_token** information.

   Log in to the host where Prometheus is located, go to the Prometheus installation directory, and save the token information of the target cluster in a file.

   |image4|

#. Configure a Prometheus monitoring job.

   The example job monitors container metrics. To monitor other metrics, you can add jobs and compile capture rules.

   .. code-block::

        - job_name: k8s_cAdvisor
          scheme: https
          bearer_token_file: k8s_token # Token file in the previous step.
          tls_config:
            insecure_skip_verify: true
          kubernetes_sd_configs:  # kubernetes automatic discovery configuration
          - role: node    # Automatic discovery of the node type
            bearer_token_file: k8s_token # Token file in the previous step
            api_server: https://192.168.0.153:5443  # API server address of the Kubernetes cluster
            tls_config:
              insecure_skip_verify: true   # Skip the authentication on the server.
          relabel_configs:  ## Modify the existing label of the target cluster before capturing metrics.
          - target_label: __address__
            replacement: 192.168.0.153:5443
            action: replace
            ## Convert metrics_path to /api/v1/nodes/${1}/proxy/metrics/cadvisor.
            # Obtain data from kubelet using the API server proxy.
          - source_labels: [__meta_kubernetes_node_name]   # Specifies the source label to be processed.
            regex: (.+)    # Matched value of the source label. (.+) indicates that any value of the source label can be matched.
            target_label: __metrics_path__     # Specifies the label to be replaced.
            replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor  # Indicates the new label, that is, the value of __metrics_path__. ${1} indicates the value that matches the regular expression, that is, node name.
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
              insecure_skip_verify: true   # Skip the authentication on the server.
          relabel_configs:  ## Modify the existing label of the target cluster before capturing metrics.
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

.. |image1| image:: /_static/images/en-us_image_0000001851585064.png
.. |image2| image:: /_static/images/en-us_image_0000001851585068.png
.. |image3| image:: /_static/images/en-us_image_0000001898023989.png
.. |image4| image:: /_static/images/en-us_image_0000001851743816.png
.. |image5| image:: /_static/images/en-us_image_0000001851585080.png
.. |image6| image:: /_static/images/en-us_image_0000001851743820.png
