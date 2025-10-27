:original_name: cce_bestpractice_10024.html

.. _cce_bestpractice_10024:

Protecting a CCE Cluster Against Overload
=========================================

Cluster overload occurs when a Kubernetes cluster's compute, storage, or network resources exceed its processing capacity, leading to exhaustion of key control plane components (like etcd and kube-apiserver) or worker nodes. This can severely degrade cluster performance or even cause operational failures. To prevent this, proactive overload protection is essential. Policies like overload control and LIST request optimization help maintain the stability of core services, ensuring that sudden spikes in loads do not result in service disruptions. This section explores the causes and impacts of cluster overload, explains CCE cluster overload control, and provides best practices for maintaining stability.

Causes of Cluster Overload
--------------------------

Cluster overload affects stability and service continuity within a Kubernetes environment. Common causes include:

-  **Resource request exceeding cluster capacity**: Core components on the cluster control plane include etcd and kube-apiserver. etcd is a backend database that stores all cluster data. kube-apiserver acts as the entry to the control plane and processes requests. kube-apiserver caches cluster data to lessen the burden on etcd. Other core components in the cluster also cache various resources and monitor changes to these resources. When the demand for compute, network, and storage resources surpasses the cluster capacity, these components become heavily loaded. If this load exceeds a certain threshold, the cluster becomes overloaded.

-  **Lots of data queries (such as multiple LIST requests or a single request retrieving large amounts of data)**: If a client obtains pod data using field selectors, but kube-apiserver cache does not contain the requested information, the client must fetch the full pod data from etcd, since etcd cannot get data by field. Once retrieved, kube-apiserver deserializes the pod data into structured objects in memory, traverses each pod to match the requested fields, and returns the obtained serialized results. When many concurrent queries occur, resource utilization of each component increases sharply, leading to issues such as etcd latency spikes, OOM errors in kube-apiserver, and control loop imbalances. Consequently, the entire cluster becomes overloaded.


   .. figure:: /_static/images/en-us_image_0000002467716521.png
      :alt: **Figure 1** Example of a large amount of data obtained from a client

      **Figure 1** Example of a large amount of data obtained from a client

Impacts of Cluster Overload
---------------------------

When a cluster experiences overload, Kubernetes API response delay increase, and resource utilization on control plane nodes rises, affecting both the control plane and related services. Below are key areas impacted:

-  **Kubernetes resource management**: Operations such as creating, deleting, updating, or obtaining Kubernetes resources may fail.
-  **Kubernetes leader election**: Overload can prevent a leader node lease renewal from completing on time. If the lease expires, the leader node loses its leadership role, triggering re-election. This can cause temporary service interruptions, task migrations, scheduling delays, and fluctuations in cluster performance.
-  **Cluster management failures**: Severe overload may make a cluster unavailable, preventing management operations like node creation or deletion.

CCE Overload Control
--------------------

-  **Overload control**: CCE clusters have supported overload control since v1.23, which reduces the number of LIST requests outside the system when the control plane experiences high resource usage pressure. To use this function, enable overload control for your clusters. For details, see :ref:`Enabling Overload Control for a Cluster <cce_10_0602>`.
-  **Optimized processes on LIST requests**: Starting from CCE clusters of v1.23.8-r0 and v1.25.3-r0, processes on LIST requests have been optimized. Even if a client does not specify the **resourceVersion** parameter, kube-apiserver responds to requests based on its cache to avoid additional etcd queries and ensure that the response data is up to date. Additionally, namespace indexes are now added to the kube-apiserver cache. This allows clients to request specified resources in a namespace without needing to fetch full data for that namespace. This effectively reduces the response delay and control plane memory overhead.
-  **Refined traffic limit policy on the server**: The API Priority and Fairness (APF) feature is used to implement fine-grained control on concurrent requests. For details, see `API Priority and Fairness <https://kubernetes.io/docs/concepts/cluster-administration/flow-control/>`__.

Configuration Suggestions
-------------------------

When running services in Kubernetes clusters, the cluster performance and availability are influenced by various factors, including the cluster scale, number and volume of resources, and resource access. CCE has optimized cluster performance and availability based on cloud native practices and has developed suggestions to protect against cluster overload. You can use these suggestions to ensure that your services run stably and reliably over the long term.

+-------------+---------------------------------------------------------------------------------------------+
| Category    | Reference                                                                                   |
+=============+=============================================================================================+
| Cluster     | :ref:`Keeping the Cluster Version Up to Date <cce_bestpractice_10024__section88153516598>`  |
+-------------+---------------------------------------------------------------------------------------------+
|             | :ref:`Enabling Overload Control <cce_bestpractice_10024__section1962889101>`                |
+-------------+---------------------------------------------------------------------------------------------+
|             | :ref:`Changing the Cluster Scale <cce_bestpractice_10024__section1218480352>`               |
+-------------+---------------------------------------------------------------------------------------------+
|             | :ref:`Controlling Data Volume of Resources <cce_bestpractice_10024__section19545122113564>` |
+-------------+---------------------------------------------------------------------------------------------+
|             | :ref:`Using Multiple Clusters <cce_bestpractice_10024__section96501146193515>`              |
+-------------+---------------------------------------------------------------------------------------------+
| O&M         | :ref:`Enabling Observability <cce_bestpractice_10024__section128547158020>`                 |
+-------------+---------------------------------------------------------------------------------------------+
|             | :ref:`Clearing Unused Resources <cce_bestpractice_10024__section16249619707>`               |
+-------------+---------------------------------------------------------------------------------------------+
| Application | :ref:`Optimizing the Client Access Mode <cce_bestpractice_10024__section203538231209>`      |
+-------------+---------------------------------------------------------------------------------------------+

.. _cce_bestpractice_10024__section88153516598:

Keeping the Cluster Version Up to Date
--------------------------------------

As the CCE cluster version evolves, new overload protection features and optimizations are regularly introduced. It is recommended that you promptly upgrade your clusters to the latest version. For details, see :ref:`Cluster Upgrade Overview <cce_10_0197>`.

.. _cce_bestpractice_10024__section1962889101:

Enabling Overload Control
-------------------------

After overload control is enabled, concurrent LIST requests outside the system will be dynamically controlled based on the resource demands received by master nodes to ensure the stable running of the master nodes and the cluster.

For details, see :ref:`Enabling Overload Control for a Cluster <cce_10_0602>`.

.. _cce_bestpractice_10024__section1218480352:

Changing the Cluster Scale
--------------------------

If the resource usage on the master nodes in a cluster remains high for a long time, for example, the memory usage is greater than 85%, it is recommended that you promptly increase the cluster management scale. This will prevent the cluster from becoming overloaded during sudden traffic surges. For details, see :ref:`Changing a Cluster Scale <cce_10_0403>`.

.. note::

   -  The larger the cluster management scale, the higher the specifications and performance of the master nodes.
   -  The CCE cluster management scale is the maximum number of nodes that a cluster can manage. It is used as a reference during service deployment planning, and the actual quantity of nodes in use may not reach the limit. The actual scale depends on various factors, including the type, quantity, and size of resource objects in the cluster, as well as the amount of external access to the cluster control plane.

.. _cce_bestpractice_10024__section19545122113564:

Controlling Data Volume of Resources
------------------------------------

When the resource data volume in a cluster is too large, it can negatively impact etcd performance, including data read and write latency. Additionally, if the data volume of a single type of resource is too large, the control plane consumes a significant number of resources when a client requests all the resources. To avoid these issues, it is recommended that you keep both the etcd data volume and the data volume of a single type of resources under control.

.. table:: **Table 1** Recommended maximum etcd data volume for different cluster scales

   +------------------------------------------------+----------+-----------+------------+------------+
   | Cluster Scale                                  | 50 Nodes | 200 Nodes | 1000 Nodes | 2000 Nodes |
   +================================================+==========+===========+============+============+
   | Total etcd data capacity                       | 500 MiB  | 1 GiB     | 4 GiB      | 8 GiB      |
   +------------------------------------------------+----------+-----------+------------+------------+
   | etcd data volume of a single type of resources | 50 MiB   | 100 MiB   | 400 MiB    | 800 MiB    |
   +------------------------------------------------+----------+-----------+------------+------------+

.. _cce_bestpractice_10024__section96501146193515:

Using Multiple Clusters
-----------------------

The Kubernetes architecture has a performance bottleneck, meaning that the scale of a single cluster cannot be expanded indefinitely. If your cluster has 2,000 worker nodes, it is necessary to split the services and deploy them across multiple clusters. If you encounter any issues with splitting a cluster, submit a service ticket for technical support.

.. _cce_bestpractice_10024__section128547158020:

Enabling Observability
----------------------

Observability is crucial for maintaining the reliability and stability of clusters. By using monitoring, alarms, and logs, administrators can gain a better understanding of the clusters' performance, promptly identify any issues, and take corrective action in a timely manner.

**Monitoring configurations**

-  You can check the monitoring information about master nodes on the **Overview** page of the CCE cluster console.

.. _cce_bestpractice_10024__section16249619707:

Clearing Unused Resources
-------------------------

To prevent a large number of pending pods from consuming extra resources on the control plane, it is recommended that you promptly clear up Kubernetes resources that are no longer in use, such as ConfigMaps, Secrets, and PVCs.

.. table:: **Table 2** Resource limits

   +-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Category                                  | Description                                                                                                                                                                                                                                                                                                                                          | Recommended Solution                                                                                                                                                                                                                                     |
   +===========================================+======================================================================================================================================================================================================================================================================================================================================================+==========================================================================================================================================================================================================================================================+
   | Number of Services in a namespace         | In a Kubernetes cluster, kubelet transforms the Service information specified in the cluster into environment variables and then injects them into the pod that is running on the node. This enables the pod to access these environment variables and locate the Service, facilitating communication with it.                                       | Do not configure these environment variables and set **enableServiceLinks** in **podSpec** to **false**. For details, see `Accessing the Service <https://kubernetes.io/docs/tutorials/services/connect-applications-service/#accessing-the-service>`__. |
   |                                           |                                                                                                                                                                                                                                                                                                                                                      |                                                                                                                                                                                                                                                          |
   |                                           | However, if there are numerous Services defined in a namespace, kubelet may have to inject a larger number of environment variables into the pod, which could potentially delay the pod's startup process. In severe cases, the pod may even fail to start.                                                                                          |                                                                                                                                                                                                                                                          |
   +-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Total number of Services in a cluster     | If the number of Services increases, the number of kube-proxy network rules increases, affecting network performance.                                                                                                                                                                                                                                | It is recommended that the total number of Services be less than or equal to 10,000.                                                                                                                                                                     |
   |                                           |                                                                                                                                                                                                                                                                                                                                                      |                                                                                                                                                                                                                                                          |
   |                                           |                                                                                                                                                                                                                                                                                                                                                      | It is recommended that the total number of LoadBalancer Services be less than or equal to 500.                                                                                                                                                           |
   +-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Maximum number of endpoints in a Service  | When a Service is associated with a large number of endpoints, each time the endpoints are updated, a large amount of data is synchronized between the control plane component kube-apiserver and kube-proxy on the node, which may cause significant pressure on network and system resources.                                                      | It is recommended that the number of backend pods of a single Service endpoint be less than or equal to 3000.                                                                                                                                            |
   |                                           |                                                                                                                                                                                                                                                                                                                                                      |                                                                                                                                                                                                                                                          |
   |                                           | .. note::                                                                                                                                                                                                                                                                                                                                            |                                                                                                                                                                                                                                                          |
   |                                           |                                                                                                                                                                                                                                                                                                                                                      |                                                                                                                                                                                                                                                          |
   |                                           |    Kubernetes provides the `EndpointSlices <https://kubernetes.io/docs/concepts/services-networking/endpoint-slices/>`__ feature to efficiently update endpoints. This feature is enabled by default in clusters of v1.19 and later versions, and enters the stable state in clusters of v1.21.                                                      |                                                                                                                                                                                                                                                          |
   +-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Total number of endpoints of all Services | If a Service has an excessive number of endpoints, the API server will have to handle a larger amount of data, potentially leading to a high workload on the API server and a decline in network performance.                                                                                                                                        | It is recommended that the total number of endpoints associated with all Services be less than or equal to 64,000.                                                                                                                                       |
   +-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Number of pending pods                    | When there is a high number of pods waiting to be scheduled in a cluster, new pods may remain in the pending state for an extended period, causing delays in their allocation to suitable nodes. If a pod cannot be scheduled for a prolonged period, the scheduler generates events at regular intervals, leading to an excessive number of events. | It is recommended that the total number of pending pods be less than or equal to 10,000.                                                                                                                                                                 |
   +-------------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. _cce_bestpractice_10024__section203538231209:

Optimizing the Client Access Mode
---------------------------------

-  To avoid frequent LIST queries, it is best to use the client cache mechanism when retrieving cluster resource data multiple times. It is recommended that you communicate with clusters using informers and listers. For details, see the `Go documentation <https://pkg.go.dev/k8s.io/client-go/tools/cache>`__.

   If a LIST query must be used, you can:

   -  Obtain needed data from the kube-apiserver cache first and avoid making additional queries on etcd data. For clusters earlier than v1.23.8-r0 or v1.25.3-r0, you can set **resourceVersion** to **0**. In clusters of v1.23.8-r0, v1.25.3-r0, and later versions, CCE has improved data retrieval and ensured that the cached data is up to date. By default, you can access the required data from the cache.

   -  Accurately define the query scope to avoid retrieving irrelevant data and using unnecessary resources. For example:

      .. code-block::

         # client-go Code example for obtaining pods in a specified namespace
         k8sClient.CoreV1().Pods("<your-namespace>").List(metav1.ListOptions{})
         # kubectl Command example for obtaining pods in a specified namespace
         kubectl get pods -n <your-namespace>

-  Use the more efficient Protobuf format instead of the JSON format. By default, Kubernetes returns objects serialized to JSON with content type **application/json**. This is the default serialization format for the API. However, clients may request the more efficient Protobuf representation of these objects for better performance. For details, see `Alternate representations of resources <https://kubernetes.io/docs/reference/using-api/api-concepts/#alternate-representations-of-resources>`__.
