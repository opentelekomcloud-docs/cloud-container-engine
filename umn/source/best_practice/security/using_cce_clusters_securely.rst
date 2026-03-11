:original_name: cce_bestpractice_0317.html

.. _cce_bestpractice_0317:

Using CCE Clusters Securely
===========================

To ensure security in a cluster, configure the cluster based on the suggestions below.

Using the Latest Cluster Version
--------------------------------

Kubernetes releases a major version every four months. CCE follows the same frequency as Kubernetes to release major versions. To be specific, a new CCE version is released about three months after a new Kubernetes version is released in the community. For example, Kubernetes v1.19 was released in September 2020 and CCE v1.19 was released in March 2021.

The latest cluster version has known vulnerabilities fixed and provides a more comprehensive security protection system. You are advised to select the latest cluster version when creating a cluster. Upgrade your cluster to a supported version before the current version is deprecated or removed.

Disabling Automatic Token Mounting for the Default Service Account
------------------------------------------------------------------

By default, Kubernetes associates the **default** service account to each pod. This means that a token is attached to the containers. This token can be authenticated by kube-apiserver and kubelet. In a cluster with RBAC disabled, the service account that owns the token has control permissions for the entire cluster. In a cluster with RBAC enabled, the permissions of the service account that owns the token depend on the roles associated by the administrator. The service account's token is generally used by workloads that need to access kube-apiserver, such as CoreDNS, Autoscaler, and Prometheus. For workloads that do not need to access kube-apiserver, you are advised to disable the automatic association between the service account and token.

You can use either of the following methods to disable the automatic association:

-  Set the **automountServiceAccountToken** field of the service account to **false**. After the configuration is complete, any newly created workload will not be associated with the **default** service account by default. You need to configure this parameter for each namespace as required.

   .. code-block::

      apiVersion: v1
      kind: ServiceAccount
      metadata:
        name: default
      automountServiceAccountToken: false
      ...

   When a workload needs to be associated with the service account, you should explicitly set **automountServiceAccountToken** to **true** in the workload YAML file.

   .. code-block::

      ...
       spec:
         template:
           spec:
             serviceAccountName: default
             automountServiceAccountToken: true
             ...

-  Explicitly disable the function of automatically associating the service account with workloads.

   .. code-block::

      ...
       spec:
         template:
           spec:
             automountServiceAccountToken: false
             ...

Configuring Proper Cluster Access Permissions for Users
-------------------------------------------------------

CCE allows you to create multiple IAM users. Your account can create different user groups and assign different access permissions to these user groups. When creating users, you can add them to the user groups with corresponding permissions. In this way, you can control user permissions in different regions and assign read-only permissions to certain users. You can also assign namespace permissions to users or user groups. To ensure security, assign the minimum user access permissions.

To create multiple IAM users under an account, configure the permissions of the users and namespaces properly.

Configuring Resource Quotas for a Namespace
-------------------------------------------

The total number of resources that can be allocated to each namespace should be limited. These resources include CPUs, memory, storage volumes, pods, Services, Deployments, and StatefulSets. Proper configuration can prevent excessive resources created in a namespace from affecting the stability of the entire cluster.

Configuring LimitRanges for Containers in a Namespace
-----------------------------------------------------

With resource quotas, cluster administrators can limit the use and creation of resources by namespace. In a namespace, a pod or container can use the maximum CPUs and memory defined by a resource quota. In this way, the pod or container may take up all available resources. It is advised to configure LimitRanges to restrict resource allocation in namespaces. LimitRanges can:

-  Limit the minimum and maximum resource usage of each pod or container in a namespace.

   For example, configure the maximum and minimum CPU usage for a pod in a namespace as follows:

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

   Run **kubectl -n** *<namespace>* **create -f** *cpu-constraints.yaml* to complete the creation. If the default CPU usage is not specified for the container, CCE will automatically configure the default CPU usage. This means that the default configuration is automatically added after the container is created.

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

-  Limit the maximum and minimum storage space that each PVC can request in a namespace.

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

   Run **kubectl -n** *<namespace>* **create -f** *storagelimit.yaml* to complete the creation.

Configuring Network Isolation
-----------------------------

-  Container tunnel network

   If networks need to be isolated between namespaces in a cluster or between workloads in the same namespace, you can use network policies.

-  Cloud Native 2.0 network

   For a cluster using a Cloud Native 2.0 network, you can use security groups to isolate networks between pods. For details, see :ref:`Binding a Security Group to a Workload Using a Security Group Policy <cce_10_0288>`.

-  VPC network

   Network isolation is not supported.

Enabling Webhook Authentication for kubelet
-------------------------------------------

.. important::

   CCE clusters of v1.15.6-r1 or earlier are involved, whereas versions later than v1.15.6-r1 are not.

   Upgrade the CCE cluster version to 1.13 or 1.15 and enable RBAC for the cluster. If the version is v1.13 or later, no upgrade is required.

When creating a node, you can enable kubelet authentication by injecting the **postinstall** file (by configuring the kubelet startup parameter **--authorization-node=Webhook**).

#. Create a ClusterRoleBinding.

   **kubectl create clusterrolebinding kube-apiserver-kubelet-admin --clusterrole=system:kubelet-api-admin --user=system:kube-apiserver**

#. For an existing node, log in to the node, change **authorization mode** in **/var/paas/kubernetes/kubelet/kubelet_config.yaml** to **Webhook**, and restart kubelet.

   **sed -i s/AlwaysAllow/Webhook/g /var/paas/kubernetes/kubelet/kubelet_config.yaml; systemctl restart kubelet**

#. For a new node, add the command below to the post-installation script to change the kubelet authentication mode.

   **sed -i s/AlwaysAllow/Webhook/g /var/paas/kubernetes/kubelet/kubelet_config.yaml; systemctl restart kubelet**

Uninstalling Kubernetes Web Terminal After Use
----------------------------------------------

The Kubernetes Web Terminal add-on can be used to manage CCE clusters. Keep the login password secure to prevent unexpected operation and uninstall the add-on when it is no longer needed.

Using Physical Multi-Tenancy
----------------------------

To ensure that resources of different tenants are isolated and to reduce the attack surface, a separate CCE cluster can be created for each tenant. However, this solution comes with some drawbacks.

-  High costs: It may increase the costs as more hardware or VM resources need to be purchased, and more workforce and time need to be spent on managing and maintaining these clusters.
-  Low resource utilization: Each tenant requires separate hardware or VM resources to run their clusters, which may result in low resource utilization and wastage.
-  Complex management: Managing and maintaining separate clusters for each tenant increase management complexity and may require more workforce and time.
