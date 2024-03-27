:original_name: cce_bestpractice_0317.html

.. _cce_bestpractice_0317:

Cluster Security
================

For security purposes, you are advised to configure a cluster as follows.

Using the CCE Cluster of the Latest Version
-------------------------------------------

Kubernetes releases a major version in about four months. CCE follows the same frequency as Kubernetes to release major versions. To be specific, a new CCE version is released about three months after a new Kubernetes version is released in the community. For example, Kubernetes v1.19 was released in September 2020 and CCE v1.19 was released in March 2021.

The latest cluster version has known vulnerabilities fixed or provides a more comprehensive security protection mechanism. You are advised to select the latest cluster version when creating a cluster. Before a cluster version is deprecated and removed, upgrade your cluster to a supported version.

Disabling the Automatic Token Mounting Function of the Default Service Account
------------------------------------------------------------------------------

By default, Kubernetes associates the default service account with every pod. That is, the token is mounted to a container. The container can use this token to pass the authentication by the kube-apiserver and kubelet components. In a cluster with RBAC disabled, the service account who owns the token has the control permissions for the entire cluster. In a cluster with RBAC enabled, the permissions of the service account who owns the token depends on the roles associated by the administrator. The service account's token is generally used by workloads that need to access kube-apiserver, such as coredns, autoscaler, and prometheus. For workloads that do not need to access kube-apiserver, you are advised to disable the automatic association between the service account and token.

Two methods are available:

-  Method 1: Set the **automountServiceAccountToken** field of the service account to **false**. After the configuration is complete, newly created workloads will not be associated with the default service account by default. Configure this field for each namespace as required.

   .. code-block::

      apiVersion: v1
      kind: ServiceAccount
      metadata:
        name: default
      automountServiceAccountToken: false
      ...

   When a workload needs to be associated with a service account, explicitly set the **automountServiceAccountToken** field to **true** in the YAML file of the workload.

   .. code-block::

      ...
       spec:
         template:
           spec:
             serviceAccountName: default
             automountServiceAccountToken: true
             ...

-  Method 2: Explicitly disable the function of automatically associating with service accounts for workloads.

   .. code-block::

      ...
       spec:
         template:
           spec:
             automountServiceAccountToken: false
             ...

Configuring Proper Cluster Access Permissions for Users
-------------------------------------------------------

CCE allows you to create multiple IAM users. Your account can create different user groups, assign different access permissions to different user groups, and add users to the user groups with corresponding permissions when creating IAM users. In this way, users can control permissions on different regions and assign read-only permissions. Your account can also assign namespace-level permissions for users or user groups. To ensure security, it is advised that minimum user access permissions are assigned.

If you need to create multiple IAM users, configure the permissions of the IAM users and namespaces properly.

Configuring Resource Quotas for Cluster Namespaces
--------------------------------------------------

CCE provides resource quota management, which allows users to limit the total amount of resources that can be allocated to each namespace. These resources include CPU, memory, storage volumes, pods, Services, Deployments, and StatefulSets. Proper configuration can prevent excessive resources created in a namespace from affecting the stability of the entire cluster.

Configuring LimitRange for Containers in a Namespace
----------------------------------------------------

With resource quotas, cluster administrators can restrict the use and creation of resources by namespace. In a namespace, a pod or container can use the maximum CPU and memory resources defined by the resource quota of the namespace. In this case, a pod or container may monopolize all available resources in the namespace. You are advised to configure LimitRange to restrict resource allocation within the namespace. The LimitRange parameter has the following restrictions:

-  Limits the minimum and maximum resource usage of each pod or container in a namespace.

   For example, create the maximum and minimum CPU usage limits for a pod in a namespace as follows:

   cpu-constraints.yaml

   .. code-block::

      apiVersion: v1
      kind: LimitRange
      metadata:
        name: cpu-min-max-demo-lr
      spec:
        limits:
        - max:
            cpu: "800m"
          min:
            cpu: "200m"
          type: Container

   Then, run **kubectl -n** *<namespace>* **create -f** *cpu-constraints.yaml* to complete the creation. If the default CPU usage is not specified for the container, the platform automatically configures the default CPU usage. That is, the default configuration is automatically added after the container is created.

   .. code-block::

      ...
      spec:
        limits:
        - default:
            cpu: 800m
          defaultRequest:
            cpu: 800m
          max:
            cpu: 800m
          min:
            cpu: 200m
          type: Container

-  Limits the maximum and minimum storage space that each PersistentVolumeClaim can apply for in a namespace.

   storagelimit.yaml

   .. code-block::

      apiVersion: v1
      kind: LimitRange
      metadata:
        name: storagelimit
      spec:
        limits:
        - type: PersistentVolumeClaim
          max:
            storage: 2Gi
          min:
            storage: 1Gi

   Then, run **kubectl -n** *<namespace>* **create -f** *storagelimit.yaml* to complete the creation.

Configuring Network Isolation in a Cluster
------------------------------------------

-  Container tunnel network

   If networks need to be isolated between namespaces in a cluster or between workloads in the same namespace, you can configure network policies to isolate the networks.

-  Cloud Native Network 2.0

   In the Cloud Native Network 2.0 model, you can configure security groups to isolate networks between pods. For details, see `Security Group Policies <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_10_0288.html>`__.

-  VPC network

   Network isolation is not supported.

Enabling the Webhook Authentication Mode with kubelet
-----------------------------------------------------

.. important::

   CCE clusters of v1.15.6-r1 or earlier are involved, whereas versions later than v1.15.6-r1 are not.

   Upgrade the CCE cluster version to 1.13 or 1.15 and enable the RBAC capability for the cluster. If the version is 1.13 or later, no upgrade is required.

When creating a node, you can enable the kubelet authentication mode by injecting the **postinstall** file (by setting the kubelet startup parameter **--authorization-node=Webhook**).

#. Run the following command to create clusterrolebinding:

   **kubectl create clusterrolebinding kube-apiserver-kubelet-admin --clusterrole=system:kubelet-api-admin --user=system:kube-apiserver**

#. For an existing node, log in to the node, change **authorization mode** in **/var/paas/kubernetes/kubelet/kubelet_config.yaml** on the node to **Webhook**, and restart kubelet.

   **sed -i s/AlwaysAllow/Webhook/g /var/paas/kubernetes/kubelet/kubelet_config.yaml; systemctl restart kubelet**

#. For a new node, add the following command to the post-installation script to change the kubelet permission mode:

   **sed -i s/AlwaysAllow/Webhook/g /var/paas/kubernetes/kubelet/kubelet_config.yaml; systemctl restart kubelet**

Uninstalling web-terminal After Use
-----------------------------------

The web-terminal add-on can be used to manage CCE clusters. Keep the login password secure and uninstall the add-on when it is no longer needed.
