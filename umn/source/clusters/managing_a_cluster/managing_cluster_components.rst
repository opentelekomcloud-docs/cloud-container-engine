:original_name: cce_10_0213.html

.. _cce_10_0213:

Managing Cluster Components
===========================

Scenario
--------

CCE allows you to manage cluster parameters, through which you can let core components work under your very requirements.

Notes and Constraints
---------------------

This function is supported only in clusters of **v1.15 and later**. It is not displayed for versions earlier than v1.15.

Procedure
---------

#. Log in to the CCE console. In the navigation pane, choose **Clusters**.
#. Click |image1| next to the target cluster.
#. On the **Manage Component** page on the right, change the values of the following Kubernetes parameters:

   .. table:: **Table 1** Extended controller parameters

      +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Parameter             | Description                                                                                                                          | Value                 |
      +=======================+======================================================================================================================================+=======================+
      | enable-resource-quota | Whether to automatically create a resource quota object when creating a namespace.                                                   | Default: false        |
      |                       |                                                                                                                                      |                       |
      |                       | -  **false**: no auto creation                                                                                                       |                       |
      |                       | -  **true**: auto creation enabled For details about the resource quota defaults, see :ref:`Setting a Resource Quota <cce_10_0287>`. |                       |
      +-----------------------+--------------------------------------------------------------------------------------------------------------------------------------+-----------------------+

   .. table:: **Table 2** kube-apiserver parameters

      +----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------+
      | Parameter                              | Description                                                                                                                                                                 | Value                                   |
      +========================================+=============================================================================================================================================================================+=========================================+
      | default-not-ready-toleration-seconds   | notReady tolerance time, in seconds. NoExecute that is added by default to every pod that does not already have such a toleration.                                          | Default: 300s                           |
      +----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------+
      | default-unreachable-toleration-seconds | unreachable tolerance time, in seconds. NoExecute that is added by default to every pod that does not already have such a toleration.                                       | Default: 300s                           |
      +----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------+
      | max-mutating-requests-inflight         | Maximum number of concurrent mutating requests. When the value of this parameter is exceeded, the server rejects requests.                                                  | Default: 1000                           |
      |                                        |                                                                                                                                                                             |                                         |
      |                                        | The value **0** indicates no limitation.                                                                                                                                    |                                         |
      |                                        |                                                                                                                                                                             |                                         |
      |                                        | Manual configuration is no longer supported since cluster v1.21. The value is automatically specified based on the cluster scale.                                           |                                         |
      |                                        |                                                                                                                                                                             |                                         |
      |                                        | -  **200** for clusters with 50 or 200 nodes                                                                                                                                |                                         |
      |                                        | -  **500** for clusters with 1,000 nodes                                                                                                                                    |                                         |
      |                                        | -  **1000** for clusters with 2,000 nodes                                                                                                                                   |                                         |
      +----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------+
      | max-requests-inflight                  | Maximum number of concurrent non-mutating requests. When the value of this parameter is exceeded, the server rejects requests.                                              | Default: 2000                           |
      |                                        |                                                                                                                                                                             |                                         |
      |                                        | The value **0** indicates no limitation.                                                                                                                                    |                                         |
      |                                        |                                                                                                                                                                             |                                         |
      |                                        | Manual configuration is no longer supported since cluster v1.21. The value is automatically specified based on the cluster scale.                                           |                                         |
      |                                        |                                                                                                                                                                             |                                         |
      |                                        | -  **400** for clusters with 50 or 200 nodes                                                                                                                                |                                         |
      |                                        | -  **1000** for clusters with 1,000 nodes                                                                                                                                   |                                         |
      |                                        | -  **2000** for clusters with 2,000 nodes                                                                                                                                   |                                         |
      +----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------+
      | service-node-port-range                | Range of node port numbers.                                                                                                                                                 | Default:                                |
      |                                        |                                                                                                                                                                             |                                         |
      |                                        |                                                                                                                                                                             | 30000-32767                             |
      |                                        |                                                                                                                                                                             |                                         |
      |                                        |                                                                                                                                                                             | Options:                                |
      |                                        |                                                                                                                                                                             |                                         |
      |                                        |                                                                                                                                                                             | min>20105                               |
      |                                        |                                                                                                                                                                             |                                         |
      |                                        |                                                                                                                                                                             | max<32768                               |
      +----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------+
      | support-overload                       | Cluster overload control. If enabled, concurrent requests are dynamically controlled based on the resource pressure of master nodes to keep them and the cluster available. | -  false: Overload control is disabled. |
      |                                        |                                                                                                                                                                             | -  true: Overload control is enabled.   |
      +----------------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------+

   .. table:: **Table 3** kube-controller-manager parameters

      +---------------------------------------+-----------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Parameter                             | Description                                                                                                           | Value                 |
      +=======================================+=======================================================================================================================+=======================+
      | concurrent-deployment-syncs           | Number of Deployments that are allowed to synchronize concurrently.                                                   | Default: 5            |
      +---------------------------------------+-----------------------------------------------------------------------------------------------------------------------+-----------------------+
      | concurrent-endpoint-syncs             | Number of endpoints that are allowed to synchronize concurrently.                                                     | Default: 5            |
      +---------------------------------------+-----------------------------------------------------------------------------------------------------------------------+-----------------------+
      | concurrent-gc-syncs                   | Number of garbage collector workers that are allowed to synchronize concurrently.                                     | Default: 20           |
      +---------------------------------------+-----------------------------------------------------------------------------------------------------------------------+-----------------------+
      | concurrent-job-syncs                  | Number of jobs that can be synchronized at the same time.                                                             | Default: 5            |
      +---------------------------------------+-----------------------------------------------------------------------------------------------------------------------+-----------------------+
      | concurrent-namespace-syncs            | Number of namespaces that are allowed to synchronize concurrently.                                                    | Default: 10           |
      +---------------------------------------+-----------------------------------------------------------------------------------------------------------------------+-----------------------+
      | concurrent-replicaset-syncs           | Number of ReplicaSets that are allowed to synchronize concurrently.                                                   | Default: 5            |
      +---------------------------------------+-----------------------------------------------------------------------------------------------------------------------+-----------------------+
      | concurrent-resource-quota-syncs       | Number of resource quotas that are allowed to synchronize concurrently.                                               | Default: 5            |
      +---------------------------------------+-----------------------------------------------------------------------------------------------------------------------+-----------------------+
      | concurrent-service-syncs              | Number of Services that are allowed to synchronize concurrently.                                                      | Default: 10           |
      +---------------------------------------+-----------------------------------------------------------------------------------------------------------------------+-----------------------+
      | concurrent-serviceaccount-token-syncs | Number of service account tokens that are allowed to synchronize concurrently.                                        | Default: 5            |
      +---------------------------------------+-----------------------------------------------------------------------------------------------------------------------+-----------------------+
      | concurrent-ttl-after-finished-syncs   | Number of TTL-after-finished controller workers that are allowed to synchronize concurrently.                         | Default: 5            |
      +---------------------------------------+-----------------------------------------------------------------------------------------------------------------------+-----------------------+
      | concurrent_rc_syncs                   | Number of replication controllers that are allowed to synchronize concurrently.                                       | Default: 5            |
      +---------------------------------------+-----------------------------------------------------------------------------------------------------------------------+-----------------------+
      | horizontal-pod-autoscaler-sync-period | How often HPA audits metrics in a cluster.                                                                            | Default: 15 seconds   |
      +---------------------------------------+-----------------------------------------------------------------------------------------------------------------------+-----------------------+
      | kube-api-qps                          | Query per second (QPS) to use while talking with kube-apiserver.                                                      | Default: 100          |
      +---------------------------------------+-----------------------------------------------------------------------------------------------------------------------+-----------------------+
      | kube-api-burst                        | Burst to use while talking with kube-apiserver.                                                                       | Default: 100          |
      +---------------------------------------+-----------------------------------------------------------------------------------------------------------------------+-----------------------+
      | terminated-pod-gc-threshold           | Number of terminated pods that can exist before the terminated pod garbage collector starts deleting terminated pods. | Default: 1000         |
      |                                       |                                                                                                                       |                       |
      |                                       | If <= 0, the terminated pod garbage collector is disabled.                                                            |                       |
      +---------------------------------------+-----------------------------------------------------------------------------------------------------------------------+-----------------------+

   .. table:: **Table 4** kube-scheduler parameters

      +----------------+------------------------------------------------------------------+--------------+
      | Parameter      | Description                                                      | Value        |
      +================+==================================================================+==============+
      | kube-api-qps   | Query per second (QPS) to use while talking with kube-apiserver. | Default: 100 |
      +----------------+------------------------------------------------------------------+--------------+
      | kube-api-burst | Burst to use while talking with kube-apiserver.                  | Default: 100 |
      +----------------+------------------------------------------------------------------+--------------+

   .. table:: **Table 5** eni parameters (supported only by CCE Turbo clusters)

      +----------------------------+----------------------------------------------------------------------------------------------+-----------------------+
      | Parameter                  | Description                                                                                  | Value                 |
      +============================+==============================================================================================+=======================+
      | nic-minimum-target         | Minimum number of ENIs bound to a node at the cluster level                                  | Default: 10           |
      +----------------------------+----------------------------------------------------------------------------------------------+-----------------------+
      | nic-maximum-target         | Maximum number of ENIs pre-bound to a node at the cluster level                              | Default: 0            |
      +----------------------------+----------------------------------------------------------------------------------------------+-----------------------+
      | nic-warm-target            | Number of ENIs pre-bound to a node at the cluster level                                      | Default: 2            |
      +----------------------------+----------------------------------------------------------------------------------------------+-----------------------+
      | nic-max-above-warm-target  | Reclaim number of ENIs pre-bound to a node at the cluster level                              | Default: 2            |
      +----------------------------+----------------------------------------------------------------------------------------------+-----------------------+
      | prebound-subeni-percentage | Low threshold of the number of bound ENIs:High threshold of the number of bound ENIs         | Default: 0:0          |
      |                            |                                                                                              |                       |
      |                            | .. note::                                                                                    |                       |
      |                            |                                                                                              |                       |
      |                            |    This parameter is discarded. Use the other four dynamic preheating parameters of the ENI. |                       |
      +----------------------------+----------------------------------------------------------------------------------------------+-----------------------+

#. Click **OK**.

References
----------

-  `kube-apiserver <https://kubernetes.io/zh/docs/reference/command-line-tools-reference/kube-apiserver/>`__
-  `kube-controller-manager <https://kubernetes.io/docs/reference/command-line-tools-reference/kube-controller-manager/>`__
-  `kube-scheduler <https://kubernetes.io/zh/docs/reference/command-line-tools-reference/kube-scheduler/>`__

.. |image1| image:: /_static/images/en-us_image_0000001199757520.png
