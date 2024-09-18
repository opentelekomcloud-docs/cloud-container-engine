:original_name: cce_bestpractice_10024.html

.. _cce_bestpractice_10024:

Protecting a CCE Cluster Against Overload
=========================================

As services grow, the Kubernetes cluster scales up, putting more pressure on the control plane. If the control plane cannot handle the load, clusters may fail to provide services. This document explains the symptoms, impact, and causes of cluster overload, as well as how CCE clusters can protect against overload. It also provides recommended measures for protecting against overload.

What Is Cluster Overload?
-------------------------

An overloaded cluster can cause delays in Kubernetes API responses and increase the resource usage on master nodes. In severe cases, the APIs may fail to respond, master nodes may become unusable, and the entire cluster may malfunction.

When a cluster is overloaded, both the control plane and the services that rely on it are impacted. The following lists some scenarios that may be affected:

-  Kubernetes resource management: Creating, deleting, updating, or obtaining Kubernetes resources may fail.
-  Kubernetes distributed leader selection: In distributed applications based on Kubernetes Leases, leaders may restart due to lease renewal request timeout.

   .. note::

      For example, if the lease renewal of the controller component of the NPD add-on fails, an active/standby switchover is triggered. This means that the active instance will restart, and the standby instance will take over services, ensuring that there is no impact on services.

-  Cluster management: When a cluster is severely overloaded, it may become unavailable. In this case, cluster management operations, such as creating or deleting nodes, cannot be performed.

Common causes of cluster overload are as follows:

-  The cluster resource data volume is too large.

   etcd and kube-apiserver are two core components of the cluster control plane. etcd serves as the background database that stores all cluster data, while kube-apiserver acts as the entry point for processing requests. kube-apiserver caches cluster data to lessen the burden on etcd, and other core components in the cluster also cache various resources and monitor changes to these resources.

   However, if the cluster resource data volume is too large, the control plane resource usage remains high, leading to overload when the resource data volume exceeds the bearing capability.

-  A large amount of data is obtained from a client. For example, a large number of LIST requests are initiated or a single LIST request is sent to obtain a large amount of data.

   Assume that a client uses field selectors to obtain pod data in a cluster and needs to obtain data from etcd (although the client can also get data from the kube-apiserver cache). Data in etcd cannot be obtained by field, so kube-apiserver must get all pod data from etcd, replicate, and serialize structured pod data, and then respond to the client request.

   When the client sends a LIST request, it may need to be processed by multiple control plane components, resulting in a larger amount of data to be processed and a more complex data type. As a result, when the client gets a large amount of data, resource usages on etcd and API server remain high. If the bearing capability is exceeded, the cluster becomes overloaded.

CCE Overload Control
--------------------

-  **Overload control**: CCE clusters have supported overload control since v1.23, which reduces the number of LIST requests outside the system when the control plane experiences high resource usage pressure. To use this function, enable overload control for your clusters. For details, see :ref:`Cluster Overload Control <cce_10_0602>`.
-  **Optimized processes on LIST requests**: Starting from CCE clusters of v1.23.8-r0 and v1.25.3-r0, processes on LIST requests have been optimized. Even if a client does not specify the **resourceVersion** parameter, kube-apiserver responds to requests based on its cache to avoid additional etcd queries and ensure that the response data is up to date. Additionally, namespace indexes are now added to the kube-apiserver cache. This means that when a client requests a specified resource in a specified namespace, it no longer needs to obtain resources belonging to the namespace based on full data. This effectively reduces the response delay and control plane memory overhead.
-  **Refined traffic limiting policy on the server**: The API Priority and Fairness (APF) feature is used to implement fine-grained control on concurrent requests. For details, see `API Priority and Fairness <https://kubernetes.io/docs/concepts/cluster-administration/flow-control/>`__.

Suggestions
-----------

This section describes measures and suggestions you can take to prevent clusters from being overloaded.

Upgrading the Cluster Version
-----------------------------

As the CCE cluster version evolves, new overload protection features and optimizations are regularly introduced. It is recommended that you promptly upgrade your clusters to the latest version. For details, see :ref:`Upgrading a Cluster <cce_10_0197>`.

Enabling Overload Control
-------------------------

After overload control is enabled, concurrent LIST requests outside the system will be dynamically controlled based on the resource demands received by master nodes to ensure the stable running of the master nodes and the cluster.

For details, see :ref:`Cluster Overload Control <cce_10_0602>`.

Enabling Observability
----------------------

Observability is crucial for maintaining the reliability and stability of clusters. By using monitoring, alarms, and logs, administrators can gain a better understanding of the clusters' performance, promptly identify any issues, and take corrective action in a timely manner.

**Monitoring configurations**

-  You can check the monitoring information about master nodes on the **Overview** page of the CCE cluster console.

Controlling Data Volume of Resources
------------------------------------

When the resource data volume in a cluster is too large, it can negatively impact etcd performance, including data read and write latency. Additionally, if the data volume of a single type of resource is too large, the control plane consumes a significant number of resources when a client requests all the resources. To avoid these issues, it is recommended that you keep both the etcd data volume and the data volume of a single type of resources under control.

.. table:: **Table 1** Recommended maximum etcd data volume for different cluster scales

   +------------------------------------------------+----------+-----------+-------------+-------------+
   | Cluster Scale                                  | 50 Nodes | 200 Nodes | 1,000 Nodes | 2,000 Nodes |
   +================================================+==========+===========+=============+=============+
   | Total etcd data capacity                       | 500Mi    | 1Gi       | 4Gi         | 8Gi         |
   +------------------------------------------------+----------+-----------+-------------+-------------+
   | etcd data volume of a single type of resources | 50Mi     | 100Mi     | 400Mi       | 800Mi       |
   +------------------------------------------------+----------+-----------+-------------+-------------+

Clearing Up Unused Resources
----------------------------

To prevent a large number of pending pods from consuming extra resources on the control plane, it is recommended that you promptly clear up Kubernetes resources that are no longer in use, such as ConfigMaps, Secrets, and PVCs.

Optimizing the Client Access Mode
---------------------------------

-  To avoid frequent LIST queries, it is best to use the client cache mechanism when retrieving cluster resource data multiple times. It is recommended that you communicate with clusters using informers and listers. For details, see `client-go documentation <https://pkg.go.dev/k8s.io/client-go/tools/cache>`__.

   If a LIST query must be used, you can:

   -  Obtain needed data from the kube-apiserver cache first and avoid making additional queries on etcd data. For clusters earlier than v1.23.8-r0 and v1.25.3-r0, you can set **resourceVersion** to **0**. In clusters of v1.23.8-r0, v1.25.3-r0, and later versions, CCE has improved the way data is retrieved and ensured that the cached data is up to date. By default, you can access the required data from the cache.

   -  Accurately define the query scope to avoid retrieving irrelevant data and using unnecessary resources. For example:

      .. code-block::

         # client-go Code example for obtaining pods in a specified namespace
         k8sClient.CoreV1().Pods("<your-namespace>").List(metav1.ListOptions{})
         # kubectl Command example for obtaining pods in a specified namespace
         kubectl get pods -n <your-namespace>

-  Use the more efficient Protobuf format instead of the JSON format. By default, Kubernetes returns objects serialized to JSON with content type **application/json**. This is the default serialization format for the API. However, clients may request the more efficient Protobuf representation of these objects for better performance. For details, see `Alternate representations of resources <https://kubernetes.io/docs/reference/using-api/api-concepts/#alternate-representations-of-resources>`__.

Changing the Cluster Scale
--------------------------

If the resource usage on the master nodes in a cluster remains high for a long time, for example, the memory usage is greater than 85%, it is recommended that you promptly increase the cluster management scale. This will prevent the cluster from becoming overloaded during sudden traffic surges. For details, see :ref:`Changing Cluster Scale <cce_10_0403>`.

.. note::

   -  The performance of the master nodes improves and their specifications become higher as the management scale of a cluster increases.
   -  The CCE cluster management scale is the maximum number of nodes that a cluster can manage. It is used as a reference during service deployment planning, and the actual quantity of nodes in use may not reach the maximum number of nodes selected. The actual scale depends on various factors, including the type, quantity, and size of resource objects in the cluster, as well as the number of external accesses to the cluster control plane.

Splitting the Cluster
---------------------

The Kubernetes architecture has a performance bottleneck, meaning that the scale of a single cluster cannot be expanded indefinitely. If your cluster has 2,000 worker nodes, it is necessary to split the services and deploy them across multiple clusters. If you encounter any issues with splitting a cluster, submit a service ticket for technical support.

Summary
-------

When running services on Kubernetes clusters, their performance and availability are influenced by various factors, including the cluster scale, number and size of resources, and resource access. CCE has optimized cluster performance and availability based on cloud native practices and has developed measures to protect against cluster overload. You can use these measures to ensure that your services run stably and reliably over the long term.
