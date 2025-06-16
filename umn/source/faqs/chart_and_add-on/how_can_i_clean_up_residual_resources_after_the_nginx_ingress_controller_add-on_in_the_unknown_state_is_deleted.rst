:original_name: cce_faq_00438.html

.. _cce_faq_00438:

How Can I Clean Up Residual Resources After the NGINX Ingress Controller Add-on in the Unknown State Is Deleted?
================================================================================================================

Symptom
-------

The NGINX Ingress Controller add-on is in the unknown state, and after this add-on is uninstalled, residual components still remain.

Involved Kubernetes resources include:

-  Namespace-level resources: secret, ConfigMap, Deployment, Service, Role, RoleBinding, lease, ServiceAccount, and job
-  Cluster-level resources: ClusterRole, ClusterRoleBinding, IngressClass, and ValidatingWebhookConfiguration

Solution
--------

#. Use kubectl to access the cluster.

#. Search for related resources.

   .. code-block::

      className="nginx"
      namespace="kube-system"
      className=`if [[ ${className} == "nginx" ]]; then echo ""; else echo "-${className}";fi`
      kubectl get -n ${namespace} secret sh.helm.release.v1.cceaddon-nginx-ingress${className}.v1 cceaddon-nginx-ingress${className}-admission
      kubectl get -n ${namespace} cm cceaddon-nginx-ingress${className}-controller
      kubectl get -n ${namespace} deploy cceaddon-nginx-ingress${className}-controller cceaddon-nginx-ingress${className}-default-backend
      kubectl get -n ${namespace} svc cceaddon-nginx-ingress${className}-controller-admission cceaddon-nginx-ingress${className}-default-backend cceaddon-nginx-ingress${className}-controller
      kubectl get -n ${namespace} role cceaddon-nginx-ingress${className}
      kubectl get -n ${namespace} rolebinding cceaddon-nginx-ingress${className}
      kubectl get -n ${namespace} lease ingress-controller-leader${className}
      kubectl get -n ${namespace} serviceAccount cceaddon-nginx-ingress${className}
      kubectl get clusterRole cceaddon-nginx-ingress${className}
      kubectl get clusterRoleBinding cceaddon-nginx-ingress${className}
      kubectl get ingressClass ${className}
      kubectl get ValidatingWebhookConfiguration cceaddon-nginx-ingress${className}-admission

   **className** specifies the name of a controller. **namespace** specifies the namespace where NGINX Ingress Controller was installed.

#. Manually delete the residual resources if the preceding resources are present.
