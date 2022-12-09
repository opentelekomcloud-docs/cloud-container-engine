:original_name: cce_01_0213.html

.. _cce_01_0213:

Configuring Kubernetes Parameters
=================================

Scenario
--------

CCE clusters allow you to manage Kubernetes parameters, through which you can let core components work under your very requirements.

Notes and Constraints
---------------------

This function is supported only in clusters of **v1.15 and later**. It is not displayed for versions earlier than v1.15.

Procedure
---------

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Clusters**.
#. Choose **More** > **Configuration**.
#. On the **Configuration** page on the right, change the values of the following Kubernetes parameters:

   .. table:: **Table 1** Kubernetes parameters

      +-------------------------+----------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+---------------------+
      | Component               | Parameter                              | Description                                                                                                                              | Value               |
      +=========================+========================================+==========================================================================================================================================+=====================+
      | kube-apiserver          | default-not-ready-toleration-seconds   | notReady tolerance time, in seconds. NoExecute that is added by default to every pod that does not already have such a toleration.       | Default: 300        |
      +-------------------------+----------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+---------------------+
      |                         | default-unreachable-toleration-seconds | unreachable tolerance time, in seconds. NoExecute that is added by default to every pod that does not already have such a toleration.    | Default: 300        |
      +-------------------------+----------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+---------------------+
      |                         | max-mutating-requests-inflight         | Maximum number of concurrent mutating requests. When the value of this parameter is exceeded, the server rejects requests.               | Default: 1000       |
      |                         |                                        |                                                                                                                                          |                     |
      |                         |                                        | The value **0** indicates no limitation.                                                                                                 |                     |
      |                         |                                        |                                                                                                                                          |                     |
      |                         |                                        | Manual configuration is no longer supported since cluster version 1.21. The value is automatically specified based on the cluster scale. |                     |
      |                         |                                        |                                                                                                                                          |                     |
      |                         |                                        | -  **200** for clusters with 50 or 200 nodes                                                                                             |                     |
      |                         |                                        | -  **500** for clusters with 1000 nodes                                                                                                  |                     |
      |                         |                                        | -  **1000** for clusters with 2000 nodes                                                                                                 |                     |
      +-------------------------+----------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+---------------------+
      |                         | max-requests-inflight                  | Maximum number of concurrent non-mutating requests. When the value of this parameter is exceeded, the server rejects requests.           | Default: 2000       |
      |                         |                                        |                                                                                                                                          |                     |
      |                         |                                        | The value **0** indicates no limitation.                                                                                                 |                     |
      |                         |                                        |                                                                                                                                          |                     |
      |                         |                                        | Manual configuration is no longer supported since cluster version 1.21. The value is automatically specified based on the cluster scale. |                     |
      |                         |                                        |                                                                                                                                          |                     |
      |                         |                                        | -  **400** for clusters with 50 or 200 nodes                                                                                             |                     |
      |                         |                                        | -  **1000** for clusters with 1000 nodes                                                                                                 |                     |
      |                         |                                        | -  **2000** for clusters with 2000 nodes                                                                                                 |                     |
      +-------------------------+----------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+---------------------+
      |                         | service-node-port-range                | Range of node port numbers.                                                                                                              | Default:            |
      |                         |                                        |                                                                                                                                          |                     |
      |                         |                                        |                                                                                                                                          | 30000-32767         |
      |                         |                                        |                                                                                                                                          |                     |
      |                         |                                        |                                                                                                                                          | Options:            |
      |                         |                                        |                                                                                                                                          |                     |
      |                         |                                        |                                                                                                                                          | min>20105           |
      |                         |                                        |                                                                                                                                          |                     |
      |                         |                                        |                                                                                                                                          | max<32768           |
      +-------------------------+----------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+---------------------+
      | kube-controller-manager | concurrent-deployment-syncs            | Number of Deployments that are allowed to synchronize concurrently.                                                                      | Default: 5          |
      +-------------------------+----------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+---------------------+
      |                         | concurrent-endpoint-syncs              | Number of endpoints that are allowed to synchronize concurrently.                                                                        | Default: 5          |
      +-------------------------+----------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+---------------------+
      |                         | concurrent-gc-syncs                    | Number of garbage collector workers that are allowed to synchronize concurrently.                                                        | Default: 20         |
      +-------------------------+----------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+---------------------+
      |                         | concurrent-job-syncs                   | Number of jobs that can be synchronized at the same time.                                                                                | Default: 5          |
      +-------------------------+----------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+---------------------+
      |                         | concurrent-namespace-syncs             | Number of namespaces that are allowed to synchronize concurrently.                                                                       | Default: 10         |
      +-------------------------+----------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+---------------------+
      |                         | concurrent-replicaset-syncs            | Number of ReplicaSets that are allowed to synchronize concurrently.                                                                      | Default: 5          |
      +-------------------------+----------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+---------------------+
      |                         | concurrent-resource-quota-syncs        | Number of resource quotas that are allowed to synchronize concurrently.                                                                  | Default: 5          |
      +-------------------------+----------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+---------------------+
      |                         | concurrent-service-syncs               | Number of Services that are allowed to synchronize concurrently.                                                                         | Default: 10         |
      +-------------------------+----------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+---------------------+
      |                         | concurrent-serviceaccount-token-syncs  | Number of service account tokens that are allowed to synchronize concurrently.                                                           | Default: 5          |
      +-------------------------+----------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+---------------------+
      |                         | concurrent-ttl-after-finished-syncs    | Number of TTL-after-finished controller workers that are allowed to synchronize concurrently.                                            | Default: 5          |
      +-------------------------+----------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+---------------------+
      |                         | concurrent_rc_syncs                    | Number of replication controllers that are allowed to synchronize concurrently.                                                          | Default: 5          |
      +-------------------------+----------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+---------------------+
      |                         | horizontal-pod-autoscaler-sync-period  | How often HPA audits metrics in a cluster.                                                                                               | Default: 15 seconds |
      +-------------------------+----------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+---------------------+
      |                         | kube-api-qps                           | Query per second (QPS) to use while talking with kube-apiserver.                                                                         | Default: 100        |
      +-------------------------+----------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+---------------------+
      |                         | kube-api-burst                         | Burst to use while talking with kube-apiserver.                                                                                          | Default: 100        |
      +-------------------------+----------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+---------------------+
      | kube-scheduler          | kube-api-qps                           | Query per second (QPS) to use while talking with kube-apiserver.                                                                         | Default: 100        |
      +-------------------------+----------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+---------------------+
      |                         | kube-api-burst                         | Burst to use while talking with kube-apiserver.                                                                                          | Default: 100        |
      +-------------------------+----------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------+---------------------+

#. Click **OK**.

References
----------

-  `kube-apiserver <https://kubernetes.io/zh/docs/reference/command-line-tools-reference/kube-apiserver/>`__
-  `kube-controller-manager <https://kubernetes.io/docs/reference/command-line-tools-reference/kube-controller-manager/>`__
-  `kube-scheduler <https://kubernetes.io/zh/docs/reference/command-line-tools-reference/kube-scheduler/>`__
