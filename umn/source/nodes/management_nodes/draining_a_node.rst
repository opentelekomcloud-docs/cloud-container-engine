:original_name: cce_10_0605.html

.. _cce_10_0605:

Draining a Node
===============

Scenario
--------

After you enable nodal drainage on the console, CCE configures the node to be non-schedulable and securely evicts all pods that comply with :ref:`Rules for Draining Nodes <cce_10_0605__section14957193483118>` on the node. Subsequent new pods will not be scheduled to this node.

When a node becomes faulty, nodal drainage quickly isolates the faulty node. The pods evicted from the faulty node will be scheduled by the workload controller to other nodes that are running properly.

.. important::

   To ensure service availability during drainage, `specify a disruption budget <https://kubernetes.io/docs/tasks/run-application/configure-pdb/>`__ for your application. Otherwise, the application may become unavailable during pod rescheduling.

Prerequisites
-------------

-  A cluster is available and the cluster version meets the following requirements:

   -  v1.21: v1.21.10-r0 or later
   -  v1.23: v1.23.8-r0 or later
   -  v1.25: v1.25.3-r0 or later
   -  Versions later than v1.25

-  To drain a node as an IAM user, you must have at least one of the following permissions (for details, see :ref:`Namespace Permissions (Kubernetes RBAC-based) <cce_10_0189>`):

   -  cluster-admin (administrator): read and write permissions on all resources in all namespaces.
   -  drainage-editor: drain a node.
   -  drainage-viewer: view the nodal drainage status but cannot drain a node.

.. _cce_10_0605__section14957193483118:

Rules for Draining Nodes
------------------------

When a node is drained, all pods on the node will be safely evicted. However, CCE will take specific actions for pods that meet certain filtering criteria.

+-----------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Filter Criterion                                                                                                                                    | Forced Drainage Enabled | Forced Drainage Disabled                                                                                                                                                                       |
+=====================================================================================================================================================+=========================+================================================================================================================================================================================================+
| The `status.phase <https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#pod-phase>`__ field of the pod is **Succeeded** or **Failed**. | Deletion                | Deletion                                                                                                                                                                                       |
+-----------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| The pod is not managed by the workload controller.                                                                                                  | Deletion                | Drainage cancellation                                                                                                                                                                          |
+-----------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| The pod is managed by DaemonSet.                                                                                                                    | None                    | Drainage cancellation                                                                                                                                                                          |
|                                                                                                                                                     |                         |                                                                                                                                                                                                |
|                                                                                                                                                     |                         | .. note::                                                                                                                                                                                      |
|                                                                                                                                                     |                         |                                                                                                                                                                                                |
|                                                                                                                                                     |                         |    For clusters v1.23.18-r10, v1.25.16-r0, v1.27.16-r0, v1.28.13-r0, v1.29.8-r0, v1.30.4-r0, or later, if forcible drainage is not used for DaemonSet pods, the default operation is **None**. |
+-----------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| A volume of the emptyDir type is mounted to the pod.                                                                                                | Eviction                | Drainage cancellation                                                                                                                                                                          |
+-----------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| The pod is a `static pod <https://kubernetes.io/docs/tasks/configure-pod-container/static-pod/>`__ directly managed by kubelet                      | None                    | None                                                                                                                                                                                           |
+-----------------------------------------------------------------------------------------------------------------------------------------------------+-------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

.. note::

   The following operations may be performed on pods during node drainage:

   -  Deletion: The pod is deleted from the current node and will not be scheduled to other nodes.
   -  Eviction: The pod is deleted from the current node and rescheduled to another node.
   -  None: The pod will not be evicted or deleted.
   -  Drainage cancellation: If a pod on a node cancels drainage, the drainage process of the node is terminated and no pod is evicted or deleted.

Drainage Using the Console
--------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Nodes**. On the displayed page, click the **Nodes** tab.
#. Locate the target node and choose **More** > **Drain Node** in the **Operation** column.
#. In the **Drain Node** window, configure the parameters.

   -  **Timeout (s)**: Node drain tasks automatically fail after the specified timeout expires. A value of 0 indicates that task will not time out.
   -  **Forcible Drain**: If this function is enabled, pods managed by DaemonSet will be ignored, and pods with emptyDir volumes and pods not managed by controllers will be deleted. For details, see :ref:`Rules for Draining Nodes <cce_10_0605__section14957193483118>`.

#. Click **OK** and wait until the node drainage is complete.

Drainage Using kubectl
----------------------

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Edit the YAML file for drainage.

   The following is an example of **Drainage-test.yaml**:

   .. code-block::

      apiVersion: node.cce.io/v1
      kind: Drainage
      metadata:
        name: 192.168.1.67-1721616409999   # Drainage resource name
      spec:
        nodeName: 192.168.1.67     # Kubernetes name of the node to be drained, which can be obtained by running the kubectl get node command
        force: true
        timeout: 0

   -  **nodeName**: node to be drained. The parameter value is the node name in Kubernetes, not the name displayed on the console.

      You can run the **kubectl get node** command to obtain a node name in Kubernetes.

   -  **force**: whether to forcibly drain a node. Value **true** means that the drainage is forced, while **false** means it is not.

   -  **timeout**: timeout measured in seconds. Node drain tasks automatically fail after the specified timeout expires. A value of 0 indicates that task will not time out.

#. Create drainage resources.

   .. code-block::

      kubectl create -f Drainage-test.yaml

   If information similar to the following is displayed, the drainage resources have been created:

   .. code-block::

      drainage.node.cce.io/192.168.1.67-1721616409999 created

#. Check the result.

   .. code-block::

      kubectl get drainages 192.168.1.67-1721616409999 -o yaml

   If **phase** is **Succeeded**, the operation is successful.

   .. code-block::

      apiVersion: node.cce.io/v1
      kind: Drainage
      metadata:
        creationTimestamp: "2024-07-22T03:12:56Z"
        generation: 1
        name: 192.168.1.67-1721616409999
        resourceVersion: "2683143"
        uid: 3ec131e4-0505-4c88-8255-ef9d0eb02712
      spec:
        force: true
        nodeName: 192.168.1.67
        timeout: 0
      status:
        conditions:
        - lastTransitionTime: "2024-07-22T03:12:56Z"
          message: start to drain node
          reason: Started
          status: "True"
          type: Started
        - lastTransitionTime: "2024-07-22T03:13:26Z"
          message: node has been drained
          reason: Succeeded
          status: "True"
          type: Finished
        phase: Succeeded

Drainage Using APIs
-------------------

#. Obtain the token in the region where the cluster is located.

#. Based on the API format, find the URL for the node drainage API.

   URL of the API for draining a node:

   .. code-block::

      https://{clusterid}.Endpoint/apis/node.cce.io/v1/drainages

   -  **{clusterid}**: cluster ID, which can be obtained on the **Overview** page of the CCE console.

   -  **Endpoint**: endpoint of CCE in the region where the cluster is located.

      For details about its value, see `Regions and Endpoints <https://docs.otc.t-systems.com/regions-and-endpoints/index.html>`__.

#. Use the **POST** request method and configure request header parameters.

   .. code-block::

      curl --location --request POST 'https://{clusterid}.Endpoint/apis/node.cce.io/v1/drainages' \
      --header 'Content-Type: application/json' \
      --header 'X-Auth-Token: MIIWvw******' \
      --data @Drainage.json

   The following table lists the header parameters contained in the request.

   .. table:: **Table 1** Request header parameters

      +--------------+-----------+--------+------------------------------------------------------------+
      | Parameter    | Mandatory | Type   | Description                                                |
      +==============+===========+========+============================================================+
      | Content-Type | Yes       | String | Message body type (format), for example, application/json. |
      +--------------+-----------+--------+------------------------------------------------------------+
      | X-Auth-Token | Yes       | String | Use a token to call the API.                               |
      +--------------+-----------+--------+------------------------------------------------------------+

   **Drainage.json** is located in the current directory and contains the following content:

   .. code-block::

      {
          "apiVersion": "node.cce.io/v1",
          "kind": "Drainage",
          "metadata": {
              "name": "192.168.1.67-1721616404940"
          },
          "spec": {
              "nodeName": "192.168.1.67",
              "force": true,
              "timeout": 0
          }
      }

   -  **nodeName**: node to be drained. The parameter value is the node name in Kubernetes, not the name displayed on the console.

      You can run the **kubectl get node** command to obtain a node name in Kubernetes.

   -  **force**: whether to forcibly drain a node. Value **true** means that the drainage is forced, while **false** means it is not.

   -  **timeout**: timeout measured in seconds. Node drain tasks automatically fail after the specified timeout expires. A value of 0 indicates that task will not time out.

Cancellation Using the Console
------------------------------

.. note::

   In clusters v1.23.16-r0, v1.25.11-r0, v1.27.8-r0, v1.28.6-r0, v1.29.2-r0, or later versions, node drainage can be canceled.

   This operation will abort drainage on nodes, but workloads that have been evicted from these nodes will not be automatically migrated back.

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Nodes**. On the displayed page, click the **Nodes** tab.
#. Locate the node that is being drained and click **Cancel Drainage**.
#. In the displayed dialog box, click **OK**. The node status changes to **Drainage cancelled**. You can click **Enable Scheduling** to restore the node to the schedulable state.

Cancellation Using kubectl
--------------------------

.. note::

   In clusters v1.23.16-r0, v1.25.11-r0, v1.27.8-r0, v1.28.6-r0, v1.29.2-r0, or later versions, node drainage can be canceled.

   This operation will abort drainage on nodes, but workloads that have been evicted from these nodes will not be automatically migrated back.

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Check drainage resources.

   .. code-block::

      kubectl get drainages

   Command output:

   .. code-block::

      NAME                         AGE
      192.168.1.67-1721616409999   13s

#. Cancel drainage.

   .. code-block::

      kubectl annotate drainages 192.168.1.67-1721616409999 node.cce.io/drainage-disable=true

#. Check the result.

   .. code-block::

      kubectl get drainages 192.168.1.67-1721616409999 -o yaml

   If the command output, **phase** is changed to **Cancelled**.

   .. code-block::

      apiVersion: node.cce.io/v1
      kind: Drainage
      metadata:
        annotations:
          node.cce.io/drainage-disable: "true"
        creationTimestamp: "2024-07-22T03:12:56Z"
        generation: 1
        name: 192.168.1.67-1721616409999
        resourceVersion: "2689858"
        uid: 3ec131e4-0505-4c88-8255-ef9d0eb02712
      spec:
        force: true
        nodeName: 192.168.1.67
        timeout: 0
      status:
        conditions:
        - lastTransitionTime: "2024-07-22T03:12:56Z"
          message: start to drain node
          reason: Started
          status: "True"
          type: Started
        - lastTransitionTime: "2024-07-22T03:13:26Z"
          message: node has been drained
          reason: Succeeded
          status: "True"
          type: Finished
        - lastTransitionTime: "2024-07-22T03:37:48Z"
          message: node drainage has been cancelled
          reason: Cancelled
          status: "True"
          type: Cancelled
        phase: Cancelled

Cancellation Using APIs
-----------------------

#. Obtain the token in the region where the cluster is located.

#. Based on the API format, find the URL for the node drainage API.

   URL of the API for canceling node drainage:

   .. code-block::

      https://{clusterid}.Endpoint/apis/node.cce.io/v1/drainages/{drainageName}

   -  **{clusterid}**: cluster ID, which can be obtained on the **Overview** page of the CCE console.

   -  **Endpoint**: endpoint of CCE in the region where the cluster is located.

      For details about its value, see `Regions and Endpoints <https://docs.otc.t-systems.com/regions-and-endpoints/index.html>`__.

   -  **{drainageName}**: name of the drainage resource, which can be obtained by running the **kubectl get drainages** command.

#. Use the **PATCH** request method and configure request header parameters.

   .. code-block::

      curl --location --request PATCH 'https://{clusterid}.Endpoint/apis/node.cce.io/v1/drainages/{drainageName}' \
      --header 'Content-Type: application/merge-patch+json' \
      --header 'X-Auth-Token: MIIWvw******' \
      --data @Drainage-cancel.json

   The following table lists the header parameters contained in the request.

   .. table:: **Table 2** Request header parameters

      +--------------+-----------+--------+-----------------------------------------------------------------------------------+
      | Parameter    | Mandatory | Type   | Description                                                                       |
      +==============+===========+========+===================================================================================+
      | Content-Type | Yes       | String | Message body type. The value is **application/merge-patch+json** in PATCH format. |
      +--------------+-----------+--------+-----------------------------------------------------------------------------------+
      | X-Auth-Token | Yes       | String | Use a token to call the API.                                                      |
      +--------------+-----------+--------+-----------------------------------------------------------------------------------+

   **Drainage-cancel.json** is located in the current directory and contains the following content:

   .. code-block::

      {
          "metadata": {
              "annotations": {
                  "node.cce.io/drainage-disable": "true"
              }
          }
      }
