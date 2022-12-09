:original_name: cce_01_0287.html

.. _cce_01_0287:

Setting a Resource Quota
========================

Namespace-level resource quotas limit the amount of resources available to teams or users when these teams or users use the same cluster. The quotas include the total number of a type of objects and the total amount of compute resources (CPU and memory) consumed by the objects.

.. note::

   Quotas can be set only in clusters of v1.9 or later.

Prerequisites
-------------

-  You have created a Kubernetes cluster. For details, see :ref:`Creating a CCE Cluster <cce_01_0028>`.
-  You have created a namespace. For details, see :ref:`Creating a Namespace <cce_01_0278>`.

Usage
-----

By default, running pods can use the CPUs and memory of a node without restrictions. This means the pods in a namespace may exhaust all resources of the cluster.

Kubernetes provides namespaces for you to group workloads in a cluster. By setting resource quotas for each namespace, you can prevent resource exhaustion and ensure cluster reliability.

You can configure quotas for resources such as CPU, memory, and the number of pods in a namespace. For more information, see `Resource Quotas <https://kubernetes.io/docs/concepts/policy/resource-quotas/?spm=a2c4g.11186623.2.8.d882712bd1i8ae>`__.

The following table recommends how many pods you can configure for your clusters of different sizes.

============= ==========================
Cluster Scale Recommended Number of Pods
============= ==========================
50 nodes      2,500 pods
200 nodes     10,000 pods
1,000 nodes   30,000 pods
2,000 nodes   50,000 pods
============= ==========================

Starting from clusters of v1.21 and later, the default `Resource Quotas <https://kubernetes.io/docs/concepts/policy/resource-quotas/?spm=a2c4g.11186623.2.8.d882712bd1i8ae>`__ are created when a namespace is created. :ref:`Table 1 <cce_01_0287__table371165714613>` lists the resource quotas based on cluster specifications. You can modify them according to your service requirements.

.. _cce_01_0287__table371165714613:

.. table:: **Table 1** Default resource quotas

   ============= ==== ========== ====== ========= =======
   Cluster Scale Pod  Deployment Secret ConfigMap Service
   ============= ==== ========== ====== ========= =======
   50 nodes      2000 1000       1000   1000      1000
   200 nodes     2000 1000       1000   1000      1000
   1,000 nodes   5000 2000       2000   2000      2000
   2,000 nodes   5000 2000       2000   2000      2000
   ============= ==== ========== ====== ========= =======

Procedure
---------

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Namespaces**.

#. Select the cluster to which the namespace belongs from the **Clusters** drop-down list.

#. In the **Operation** column of a namespace, click **Manage Quota**.

   This operation cannot be performed on system namespaces **kube-system** and **kube-public**.

#. Set the resource quotas and click **OK**.

   -  **CPU (cores)**: maximum number of CPU cores that can be allocated to workload pods in the namespace.
   -  **Memory (MiB)**: maximum amount of memory that can be allocated to workload pods in the namespace.
   -  **StatefulSet**: maximum number of StatefulSets that can be created in the namespace.
   -  **Deployment**: maximum number of Deployments that can be created in the namespace.
   -  **Job**: maximum number of one-off jobs that can be created in the namespace.
   -  **Cron Job**: maximum number of cron jobs that can be created in the namespace.
   -  **Pod**: maximum number of pods that can be created in the namespace.
   -  **Service**: maximum number of Services that can be created in the namespace.

   .. important::

      -  After setting CPU and memory quotas for a namespace, you must specify the request and limit values of CPU and memory resources when creating a workload. Otherwise, the workload cannot be created. If the quota of a resource is set to **0**, the resource usage is not limited.
      -  Accumulated quota usage includes the resources used by CCE to create default components, such as the Kubernetes Services (which can be viewed using kubectl) created under the **default** namespace. Therefore, you are advised to set a resource quota greater than expected to reserve resource for creating default components.
