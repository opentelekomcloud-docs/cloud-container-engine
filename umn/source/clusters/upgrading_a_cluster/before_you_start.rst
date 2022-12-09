:original_name: cce_01_0302.html

.. _cce_01_0302:

Before You Start
================

Before the upgrade, you can check whether your cluster can be upgraded and which versions are available on the CCE console. For details, see :ref:`Overview <cce_01_0197>`.

Precautions
-----------

-  **Upgraded clusters cannot be rolled back. Therefore, perform the upgrade during off-peak hours to minimize the impact on your services.**
-  Do not shut down or restart nodes during cluster upgrade. Otherwise, the upgrade fails.
-  Before upgrading a cluster, disable auto scaling policies to prevent node scaling during the upgrade. Otherwise, the upgrade fails.
-  If you locally modify the configuration of a cluster node, the cluster upgrade may fail or the configuration may be lost after the upgrade. Therefore, modify the configurations on the CCE console (cluster or node pool list page) so that they will be automatically inherited during the upgrade.
-  During the cluster upgrade, the running workload services will not be interrupted, but access to the API server will be temporarily interrupted.
-  Before upgrading the cluster, check whether the cluster is healthy.
-  To ensure data security, you are advised to back up data before upgrading the cluster. During the upgrade, you are not advised to perform any operations on the cluster.
-  CCE 1.17 and later versions do not support workload scaling using the AOM service. Before and after the upgrade, switch scaling policies by referring to :ref:`Switching from AOM to HPA for Auto Scaling <cce_01_0395>`.

Notes and Constraints
---------------------

-  Currently, only CCE clusters consisting of VM nodes can be upgraded.

-  If initContainer or Istio is used in the in-place upgrade of a cluster of v1.15, pay attention to the following restrictions:

   In kubelet 1.16 and later versions, `QoS classes <https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/>`__ are different from those in earlier versions. In kubelet 1.15 and earlier versions, only containers in **spec.containers** are counted. In kubelet 1.16 and later versions, containers in both **spec.containers** and **spec.initContainers** are counted. The QoS class of a pod will change after the upgrade. As a result, the container in the pod restarts. You are advised to modify the QoS class of the service container before the upgrade to avoid this problem. For details, see :ref:`Table 1 <cce_01_0302__table10713231143911>`.

   .. _cce_01_0302__table10713231143911:

   .. table:: **Table 1** QoS class changes before and after the upgrade

      +----------------------------------------------------------+---------------------------------------------------------+-------------------------------------------------------------------+-----------------+
      | Init Container (Calculated Based on spec.initContainers) | Service Container (Calculated Based on spec.containers) | Pod (Calculated Based on spec.containers and spec.initContainers) | Impacted or Not |
      +==========================================================+=========================================================+===================================================================+=================+
      | Guaranteed                                               | Besteffort                                              | Burstable                                                         | Yes             |
      +----------------------------------------------------------+---------------------------------------------------------+-------------------------------------------------------------------+-----------------+
      | Guaranteed                                               | Burstable                                               | Burstable                                                         | No              |
      +----------------------------------------------------------+---------------------------------------------------------+-------------------------------------------------------------------+-----------------+
      | Guaranteed                                               | Guaranteed                                              | Guaranteed                                                        | No              |
      +----------------------------------------------------------+---------------------------------------------------------+-------------------------------------------------------------------+-----------------+
      | Besteffort                                               | Besteffort                                              | Besteffort                                                        | No              |
      +----------------------------------------------------------+---------------------------------------------------------+-------------------------------------------------------------------+-----------------+
      | Besteffort                                               | Burstable                                               | Burstable                                                         | No              |
      +----------------------------------------------------------+---------------------------------------------------------+-------------------------------------------------------------------+-----------------+
      | Besteffort                                               | Guaranteed                                              | Burstable                                                         | Yes             |
      +----------------------------------------------------------+---------------------------------------------------------+-------------------------------------------------------------------+-----------------+
      | Burstable                                                | Besteffort                                              | Burstable                                                         | Yes             |
      +----------------------------------------------------------+---------------------------------------------------------+-------------------------------------------------------------------+-----------------+
      | Burstable                                                | Burstable                                               | Burstable                                                         | No              |
      +----------------------------------------------------------+---------------------------------------------------------+-------------------------------------------------------------------+-----------------+
      | Burstable                                                | Guaranteed                                              | Burstable                                                         | Yes             |
      +----------------------------------------------------------+---------------------------------------------------------+-------------------------------------------------------------------+-----------------+

Performing Pre-upgrade Check
----------------------------

Before upgrading a cluster, check the health status of the cluster and nodes and ensure that they are available.

**Method 1: Use the console.**

On the CCE console, click **Resource Management** in the navigation pane, and click **Clusters** and **Nodes** separately to check whether the cluster and nodes are normal.

**Method 2: Run kubectl commands.**

#. Run the following command to verify that all cluster modules are in the Healthy state:

   **kubectl get cs**

   Information similar to the following is displayed:

   .. code-block::

       NAME                 STATUS    MESSAGE              ERROR
       scheduler            Healthy   ok
       controller-manager   Healthy   ok
       etcd-0               Healthy   {"health": "true"}
       etcd-1               Healthy   {"health": "true"}
       etcd-2               Healthy   {"health": "true"}

   .. note::

      In the command output, the value of **STATUS** must be **Healthy** for all items.

#. Run the following command to verify that all nodes are in the Ready state:

   **kubectl get nodes**

   .. note::

      All nodes must be in the **Ready** state.

   .. code-block::

       NAME                   STATUS    ROLES     AGE       VERSION
       xxx.xxx.xx.xx   Ready     <none>    38d       v1.9.7-r1
       xxx.xxx.xx.xx   Ready     <none>    38d       v1.9.7-r1
       xxx.xxx.xx.xx   Ready     <none>    38d       v1.9.7-r1

Pre-upgrade Checklist
---------------------

Before upgrading a cluster, follow the pre-upgrade checklist to identify risks and problems in advance.

.. table:: **Table 2** Cluster upgrade check items

   +------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Module     | Item                                                                                                                                                                                                                                                                                                                                          |
   +============+===============================================================================================================================================================================================================================================================================================================================================+
   | Cluster    | Check whether the node IP addresses (including EIPs) of the current cluster are used in other configurations or whitelists.                                                                                                                                                                                                                   |
   +------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   |            | Perform the pre-upgrade check.                                                                                                                                                                                                                                                                                                                |
   +------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Workload   | Record the number and status of workloads for comparison after the upgrade.                                                                                                                                                                                                                                                                   |
   +------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   |            | For the databases you use (such as Direct Connect, Redis, and MongoDB), you need to consider the changes in their whitelists, routes, or security group policies in advance.                                                                                                                                                                  |
   +------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Storage    | Record the storage status to check whether storage resources are lost after the upgrade.                                                                                                                                                                                                                                                      |
   +------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Networking | Check and back up the load balancing services and ingresses.                                                                                                                                                                                                                                                                                  |
   +------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   |            | If Direct Connect is used, check whether the upgrade causes changes in the IP addresses of nodes or pods where services are deployed. To handle changes, you need to enable routes on Direct Connect in advance.                                                                                                                              |
   +------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Add-on     | When Kubernetes 1.9 is upgraded to 1.11, the kube-dns of the cluster is uninstalled and replaced with CoreDNS. Back up the DNS address configured in kube-dns so that you can use it in CoreDNS when the domain name resolution is abnormal.                                                                                                  |
   +------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | O&M        | Private configurations: Check whether data plane passwords, certificates, and environment variables are configured for nodes or containers in the cluster before the upgrade. If a container is restarted (for example, the node is abnormal and the pod is re-scheduled), the configurations will be lost and your service will be abnormal. |
   +------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   |            | Check and back up kernel parameters or system configurations.                                                                                                                                                                                                                                                                                 |
   +------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Upgrade Backup
--------------

Currently, there are two backup modes for cluster upgrade:

-  etcd database backup: CCE automatically backs up the etcd database during the cluster upgrade.
-  Master node backup (recommended, **manual confirmation required**): On the upgrade confirmation page, click **Backup** to back up the entire master node of the cluster. The backup process uses the Cloud Backup and Recovery (CBR) service and takes about 20 minutes. If there are many cloud backup tasks at the current site, the backup time may be prolonged.
