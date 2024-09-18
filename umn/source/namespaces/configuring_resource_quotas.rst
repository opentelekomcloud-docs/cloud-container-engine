:original_name: cce_10_0287.html

.. _cce_10_0287:

Configuring Resource Quotas
===========================

Namespace-level resource quotas limit the amount of resources available to teams or users when these teams or users use the same cluster. The quotas include the total number of a type of objects and the total amount of compute resources (CPU and memory) consumed by the objects.

Usage
-----

By default, running pods can use the CPUs and memory of a node without restrictions. This means the pods in a namespace may exhaust all resources of the cluster.

Kubernetes provides namespaces for you to group workloads in a cluster. By setting resource quotas for each namespace, you can prevent resource exhaustion and ensure cluster reliability.

You can configure quotas for resources such as CPU, memory, and the number of pods in a namespace. For more information, see `Resource Quotas <https://kubernetes.io/docs/concepts/policy/resource-quotas/>`__.

The following table recommends how many pods you can configure for your clusters of different sizes.

============= ==========================
Cluster Scale Recommended Number of Pods
============= ==========================
50 nodes      2,500 pods
200 nodes     10,000 pods
1000 nodes    30,000 pods
2000 nodes    50,000 pods
============= ==========================

In clusters of v1.21 and later, the default resource quotas will be created when a namespace is created if you have enabled **enable-resource-quota** in :ref:`Cluster Configuration Management <cce_10_0213>`. :ref:`Table 1 <cce_10_0287__table371165714613>` lists the resource quotas based on cluster specifications. You can modify them according to your service requirements.

.. _cce_10_0287__table371165714613:

.. table:: **Table 1** Default resource quotas

   ============= ==== ========== ====== ========= =======
   Cluster Scale Pod  Deployment Secret ConfigMap Service
   ============= ==== ========== ====== ========= =======
   50 nodes      2000 1000       1000   1000      1000
   200 nodes     2000 1000       1000   1000      1000
   1000 nodes    5000 2000       2000   2000      2000
   2000 nodes    5000 2000       2000   2000      2000
   ============= ==== ========== ====== ========= =======

Notes and Constraints
---------------------

Kubernetes provides optimistic concurrency control (OCC), also known as optimistic locking, for frequent data updates. You can use optimistic locking by defining the **resourceVersion** field. This field is in the object metadata. This field identifies the internal version number of the object. When the object is modified, this field is modified accordingly. You can use kube-apiserver to check whether an object has been modified. When the API server receives an update request containing the **resourceVersion** field, the server compares the requested data with the resource version number of the server. If they are different, the object on the server has been modified when the update is submitted. In this case, the API server returns a conflict error (409). Obtain the server data, modify the data, and submit the data to the server again. The resource quota limits the total resource consumption of each namespace and records the resource information in the cluster. Therefore, after the **enable-resource-quota** option is enabled, the probability of resource creation conflicts increases in large-scale concurrency scenarios, affecting the performance of batch resource creation.

Procedure
---------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, click **Namespaces**.

#. Click **Quota Management** next to the target namespace.

   This operation cannot be performed on system namespaces **kube-system** and **kube-public**.

#. Set the resource quotas and click **OK**.

   .. important::

      -  After setting CPU and memory quotas for a namespace, you must specify the request and limit values of CPU and memory resources when creating a workload. Otherwise, the workload cannot be created. If the quota of a resource is set to **0**, the resource usage is not limited.
      -  Accumulated quota usage includes the resources used by CCE to create default components, such as the Kubernetes Services (which can be viewed using kubectl) created under the **default** namespace. Therefore, you are advised to set a resource quota greater than expected to reserve resource for creating default components.
