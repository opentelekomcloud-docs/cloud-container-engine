:original_name: cce_whsnew_0010.html

.. _cce_whsnew_0010:

Kubernetes 1.19 (EOM) Release Notes
===================================

CCE has passed the Certified Kubernetes Conformance Program and is a certified Kubernetes offering. This section describes the updates in CCE Kubernetes 1.19.

Resource Changes and Deprecations
---------------------------------

**Kubernetes v1.19 Release Notes**

-  vSphere in-tree volumes can be migrated to vSphere CSI drivers. The in-tree vSphere Volume plugin is no longer used and will be deleted in later versions.
-  **apiextensions.k8s.io/v1beta1** has been deprecated. You are advised to use **apiextensions.k8s.io/v1**.
-  **apiregistration.k8s.io/v1beta1** has been deprecated. You are advised to use **apiregistration.k8s.io/v1**.
-  **authentication.k8s.io/v1beta1** and **authorization.k8s.io/v1beta1** have been deprecated and will be removed from Kubernetes 1.22. You are advised to use **authentication.k8s.io/v1** and **authorization.k8s.io/v1**.
-  **autoscaling/v2beta1** has been deprecated. You are advised to use **autoscaling/v2beta2**.
-  **coordination.k8s.io/v1beta1** has been deprecated in Kubernetes 1.19 and will be removed from version 1.22. You are advised to use **coordination.k8s.io/v1**.
-  kube-apiserver: The **componentstatus** API has been deprecated.
-  kubeadm: The **kubeadm config view** command has been deprecated and will be deleted in later versions. Use **kubectl get cm -o yaml -n kube-system kubeadm-config** to directly obtain the kubeadm configuration.
-  kubeadm: The **kubeadm alpha kubelet config enable-dynamic** command has been deprecated.
-  kubeadm: The **--use-api** flag in the **kubeadm alpha certs renew** command has been deprecated.
-  Kubernetes no longer supports **hyperkube** image creation.
-  The **--export** flag is removed from the **kubectl get** command.
-  The alpha feature **ResourceLimitsPriorityFunction** has been deleted.
-  **storage.k8s.io/v1beta1** has been deprecated. You are advised to use **storage.k8s.io/v1**.

**Kubernetes v1.18 Release Notes**

-  kube-apiserver

   -  All resources in the **apps/v1beta1** and **apps/v1beta2** API versions are no longer served. You can use the **apps/v1** API version.
   -  DaemonSets, Deployments, and ReplicaSets in the **extensions/v1beta1** API version are no longer served. You can use the **apps/v1** API version.
   -  NetworkPolicies in the **extensions/v1beta1** API version are no longer served. You can use the **networking.k8s.io/v1** API version.
   -  PodSecurityPolicies in the **extensions/v1beta1** API version are no longer served. Migrate to use the **policy/v1beta1** API version.

-  kubelet

   -  **--redirect-container-streaming** is not recommended and will be deprecated in v1.20.
   -  The resource measurement endpoint **/metrics/resource/v1alpha1** and all measurement standards under this endpoint have been deprecated. Use the measurement standards under the endpoint **/metrics/resource** instead:

      -  scrape_error --> scrape_error
      -  node_cpu_usage_seconds_total --> node_cpu_usage_seconds
      -  node_memory_working_set_bytes --> node_memory_working_set_bytes
      -  container_cpu_usage_seconds_total --> container_cpu_usage_seconds
      -  container_memory_working_set_bytes --> container_memory_working_set_bytes
      -  scrape_error --> scrape_error

   -  In future releases, kubelet will no longer create the target directory **CSI NodePublishVolume** according to the CSI specifications. You may need to update the CSI driver accordingly to correctly create and process the target path.

-  kube-proxy

   -  You are not advised to use the **--healthz-port** and **--metrics-port** flags. Use **--healthz-bind-address** and **--metrics-bind-address** instead.
   -  The **EndpointSliceProxying** function option is added to control the use of EndpointSlices in kube-proxy. This function is disabled by default.

-  kubeadm

   -  The **--kubelet-version** flag of **kubeadm upgrade node** has been deprecated and will be deleted in later versions.
   -  The **--use-api** flag in the **kubeadm alpha certs renew** command has been deprecated.
   -  kube-dns has been deprecated and will no longer be supported in future versions.
   -  The ClusterStatus structure in the kubeadm-config ConfigMap has been deprecated and will be deleted in later versions.

-  kubectl

   -  You are not advised to use boolean and unset values for **--dry-run**. **server|client|none** is used in the new version.
   -  **--server-dry-run** has been deprecated for **kubectl apply** and replaced by **--dry-run=server**.

-  add-ons

The cluster-monitoring add-on is deleted.

-  kube-scheduler

   -  The **scheduling_duration_seconds** metric has been deprecated.
   -  The **scheduling_algorithm_predicate_evaluation_seconds** and **scheduling_algorithm_priority_evaluation_seconds** metrics are no longer used and are replaced by **framework_extension_point_duration_seconds[extension_point="Filter"]** and **framework_extension_point_duration_seconds[extension_point="Score"]**.
   -  The scheduler policy AlwaysCheckAllPredictes has been deprecated.

-  Other changes

   -  The k8s.io/node-api component is no longer updated. Instead, you can use the **RuntimeClass** type in **k8s.io/api** and the generated clients in **k8s.io/client-go**.
   -  The **client** label has been deleted from **apiserver_request_total**.

References
----------

For more details about the performance comparison and function evolution between Kubernetes 1.19 and other versions, see the following documents:

-  `Kubernetes v1.19.0 Release Notes <https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.19.md#changes>`__
-  `Kubernetes v1.18.0 Release Notes <https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#changes>`__
