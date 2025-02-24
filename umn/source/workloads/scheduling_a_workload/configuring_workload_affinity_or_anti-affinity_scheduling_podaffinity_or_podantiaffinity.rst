:original_name: cce_10_0893.html

.. _cce_10_0893:

Configuring Workload Affinity or Anti-affinity Scheduling (podAffinity or podAntiAffinity)
==========================================================================================

Kubernetes offers workload affinity and anti-affinity scheduling, which allows for flexible scheduling of new workloads on either related or unrelated nodes. This results in improved cluster resource utilization.

For example, frontend workload pods and backend workload pods that frequently communicate with each other can be preferentially scheduled to the same node or AZ to minimize network latency. The process of workload affinity/anti-affinity is as follows:

#. Nodes are classified using node labels based on topology keys (**topologyKey**).

   For example, if **topologyKey** is **prefer**, nodes will be classified using node label **prefer**. Nodes labeled with **prefer=true** will be assigned to topology key 1, while those labeled with **prefer=false** will be assigned to topology key 2. Nodes without a **prefer** label will be assigned to topology key 3.

#. Identify the affinity/anti-affinity workloads based on the workload labels and operators.

   For example, the label selector filters the workloads that have been labeled with **app=backend**.

#. For affinity scheduling, the scheduler chooses the topology key where the target workload is located, while for anti-affinity scheduling, it selects a topology key where the target workload is not present.

   In this example, the workload labeled with **app=backend** is assigned to topology key 1. Therefore, workloads that have an affinity with the **app=backend** workload can be scheduled to topology key 1. Conversely, workloads that have an anti-affinity with the **app=backend** workload can only be scheduled to topology key 2 or 3.


.. figure:: /_static/images/en-us_image_0000002065480278.png
   :alt: **Figure 1** Workload affinity or anti-affinity scheduling

   **Figure 1** Workload affinity or anti-affinity scheduling

Using the CCE Console
---------------------

In this example, a backend workload with the **app=backend** label has been created in the cluster. This label can be used for workload affinity or anti-affinity scheduling, allowing the newly created frontend workload labeled with **app=frontend** to be deployed on the same node as the backend workload. Both workloads run in topology key **kubernetes.io/hostname**. When you use **kubernetes.io/hostname** for topology classification, only one node can be assigned to each topology key because each node has a unique hostname. This enables workloads to be scheduled on the same node when workload affinity is enabled.

#. Log in to the CCE console.

#. Click the cluster name to go to the cluster console, choose **Workloads** in the navigation pane, and click the **Create Workload** in the upper right corner.

#. In **Advanced Settings**, choose **Scheduling** and select a policy for **Load Affinity**. In this example, **Custom policies** is selected. For details about how to create a workload, see :ref:`Creating a Workload <cce_10_0673>`.

   .. table:: **Table 1** Scheduling policies

      +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Parameter             | Description                                                                                                                                                                                                                                                                                                                             | Example               |
      +=======================+=========================================================================================================================================================================================================================================================================================================================================+=======================+
      | Load affinity         | -  **Not configured**: No load affinity policy is configured.                                                                                                                                                                                                                                                                           | Custom policies       |
      |                       | -  **Multi-AZ deployment preferred**: Workload pods are **preferentially** scheduled to nodes in different AZs through pod anti-affinity. The AZs serve as topology keys in this process.                                                                                                                                               |                       |
      |                       | -  **Forcible multi-AZ deployment**: Workload pods are **forcibly** scheduled to nodes in different AZs through pod anti-affinity. The AZs serve as topology keys in this process. When this scheduling policy is used, if there are fewer nodes than pods or node resources are insufficient, the extra pods will fail to run.         |                       |
      |                       | -  **Custom policies**: allow flexible scheduling of workload pods based on pod labels. For details about the supported scheduling policies, see :ref:`Table 2 <cce_10_0893__table1186153412529>`. Select a proper policy type and add a policy. For details about the parameters, see :ref:`Table 3 <cce_10_0893__table113899313578>`. |                       |
      +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+

#. Select a proper load affinity rule and click |image1| to add a scheduling policy. In this example, a scheduling policy is added in the **Required** area under **Workload Affinity**. This policy specifies that the newly created workload can only be scheduled to a node if a workload with specific labels is already running on that node.

   .. _cce_10_0893__table1186153412529:

   .. table:: **Table 2** Load affinity policies

      +------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------+
      | Policy                 | Rule Type       | Description                                                                                                                                                                                                                                                                                                                                                                                   | Example         |
      +========================+=================+===============================================================================================================================================================================================================================================================================================================================================================================================+=================+
      | Workload affinity      | Required        | Hard constraint, which corresponds to **requiredDuringSchedulingIgnoredDuringExecution** in YAML for specifying the conditions that must be met.                                                                                                                                                                                                                                              | Required        |
      |                        |                 |                                                                                                                                                                                                                                                                                                                                                                                               |                 |
      |                        |                 | Select pods that require affinity by label. If such pods already run on a node in the topology key, the scheduler will **forcibly** schedule the created pods to that topology key.                                                                                                                                                                                                           |                 |
      |                        |                 |                                                                                                                                                                                                                                                                                                                                                                                               |                 |
      |                        |                 | .. note::                                                                                                                                                                                                                                                                                                                                                                                     |                 |
      |                        |                 |                                                                                                                                                                                                                                                                                                                                                                                               |                 |
      |                        |                 |    If multiple affinity rules are configured, multiple labels will be used to filter pods that require affinity, and the newly created pods must be affinity with all pods that meet the label filtering conditions. In this way, all pods that meet the label filtering conditions locate in the same topology key for scheduling.                                                           |                 |
      +------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------+
      |                        | Preferred       | Soft constraint, which corresponds to **preferredDuringSchedulingIgnoredDuringExecution** in YAML for specifying the conditions that need to be met as much as possible.                                                                                                                                                                                                                      |                 |
      |                        |                 |                                                                                                                                                                                                                                                                                                                                                                                               |                 |
      |                        |                 | Select pods that require affinity by label. If such pods already run on a node in the topology key, the scheduler will **preferentially** schedule the created pods to that topology key.                                                                                                                                                                                                     |                 |
      |                        |                 |                                                                                                                                                                                                                                                                                                                                                                                               |                 |
      |                        |                 | .. note::                                                                                                                                                                                                                                                                                                                                                                                     |                 |
      |                        |                 |                                                                                                                                                                                                                                                                                                                                                                                               |                 |
      |                        |                 |    If multiple affinity rules are configured, multiple labels will be used to filter pods that require affinity, and the newly created pods will be preferentially to be affinity with multiple pods that meet the label filtering conditions. However, even if no pod meets the label filter conditions, a topology key will be selected for scheduling.                                     |                 |
      +------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------+
      | Workload anti-affinity | Required        | Hard constraint, which corresponds to **requiredDuringSchedulingIgnoredDuringExecution** in YAML for specifying the conditions that must be met.                                                                                                                                                                                                                                              | None            |
      |                        |                 |                                                                                                                                                                                                                                                                                                                                                                                               |                 |
      |                        |                 | Select one or more pods that require anti-affinity by label. If such pods already run on a node in the topology key, the scheduler will **not** schedule the created pods to that topology key.                                                                                                                                                                                               |                 |
      |                        |                 |                                                                                                                                                                                                                                                                                                                                                                                               |                 |
      |                        |                 | .. note::                                                                                                                                                                                                                                                                                                                                                                                     |                 |
      |                        |                 |                                                                                                                                                                                                                                                                                                                                                                                               |                 |
      |                        |                 |    If multiple anti-affinity rules are configured, multiple labels will be used to filter pods that require anti-affinity, and the newly created pods must be anti-affinity with all pods that meet the label filtering conditions. In this way, all the topology keys where the pods that meet the label filtering conditions locate will not be scheduled.                                  |                 |
      +------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------+
      |                        | Preferred       | Soft constraint, which corresponds to **preferredDuringSchedulingIgnoredDuringExecution** in YAML for specifying the conditions that need to be met as much as possible.                                                                                                                                                                                                                      |                 |
      |                        |                 |                                                                                                                                                                                                                                                                                                                                                                                               |                 |
      |                        |                 | Select one or more pods that require anti-affinity by label. If such pods already run on a node in the topology key, the scheduler will **preferentially** schedule the created pods to other topology keys.                                                                                                                                                                                  |                 |
      |                        |                 |                                                                                                                                                                                                                                                                                                                                                                                               |                 |
      |                        |                 | .. note::                                                                                                                                                                                                                                                                                                                                                                                     |                 |
      |                        |                 |                                                                                                                                                                                                                                                                                                                                                                                               |                 |
      |                        |                 |    If multiple anti-affinity rules are configured, multiple labels will be used to filter pods that require anti-affinity, and the newly created pods will be preferentially to be anti-affinity with multiple pods that meet the label filtering conditions. However, even if all topology keys involve the pods that require anti-affinity, a topology key will be selected for scheduling. |                 |
      +------------------------+-----------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------+

#. In the dialog box that is displayed on the right, click **Add Policy** to configure rules for filtering node labels.

   .. _cce_10_0893__table113899313578:

   .. table:: **Table 3** Parameters for configuring load affinity/anti-affinity scheduling policies

      +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------+
      | Parameter             | Description                                                                                                                                                                                                                                                                                                                                          | Example                |
      +=======================+======================================================================================================================================================================================================================================================================================================================================================+========================+
      | Weight                | This parameter is available only in a **preferred** scheduling policy. Weights range from 1 to 100 and are taken into account as an extra scoring factor during scheduling. The scheduler combines the weight with other priority functions of the node to determine the final score and then assigns pods to the node with the highest total score. | None                   |
      +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------+
      | Namespace             | Namespace for which the scheduling policy takes effect.                                                                                                                                                                                                                                                                                              | default                |
      +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------+
      | Topology Key          | A topology key (**topologyKey**) determines the range of nodes to be scheduled based on node labels, identifies affinity/anti-affinity objects based on the labels and operators, and performs scheduling based on the topology key where the target object is located.                                                                              | kubernetes.io/hostname |
      |                       |                                                                                                                                                                                                                                                                                                                                                      |                        |
      |                       | -  For example, if the node label is **kubernetes.io/hostname**, the label value will be a node name. Nodes with different names are assigned to different topology keys. This allows for workload affinity scheduling on a single node, as each topology key contains only one node.                                                                |                        |
      |                       |                                                                                                                                                                                                                                                                                                                                                      |                        |
      |                       | -  If the specified label is **kubernetes.io/os**, the label value will be a node OS. Nodes running different OSs are assigned to different topology keys. This allows for workload affinity scheduling on multiple nodes, as each topology key contains multiple nodes.                                                                             |                        |
      |                       |                                                                                                                                                                                                                                                                                                                                                      |                        |
      |                       |    For example, if pods that meet the load affinity rule are running on a node in a topology key, all nodes in the topology key can be scheduled.                                                                                                                                                                                                    |                        |
      +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------+
      | Label Key             | When configuring a workload affinity or anti-affinity policy, enter the workload label to be matched.                                                                                                                                                                                                                                                | app                    |
      |                       |                                                                                                                                                                                                                                                                                                                                                      |                        |
      |                       | Both default labels and custom labels are supported.                                                                                                                                                                                                                                                                                                 |                        |
      +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------+
      | Operator              | The following operators are supported:                                                                                                                                                                                                                                                                                                               | In                     |
      |                       |                                                                                                                                                                                                                                                                                                                                                      |                        |
      |                       | -  **In**: The label of the affinity or anti-affinity object is in the label value list (**values** field).                                                                                                                                                                                                                                          |                        |
      |                       | -  **NotIn**: The label of the affinity or anti-affinity object is not in the label value list (**values** field).                                                                                                                                                                                                                                   |                        |
      |                       | -  **Exists**: The affinity or anti-affinity object has a specified label key.                                                                                                                                                                                                                                                                       |                        |
      |                       | -  **DoesNotExist**: The affinity or anti-affinity object does not have a specified label key.                                                                                                                                                                                                                                                       |                        |
      +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------+
      | Label Value           | When configuring a workload affinity or anti-affinity policy, enter the value of the workload label.                                                                                                                                                                                                                                                 | backend                |
      +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+------------------------+

#. After the scheduling policy is added, click **Create Workload**.

#. Verify that all pods run on the target node.

   a. In the navigation pane, choose **Workloads**.
   b. Click the workload name to enter its details page. On the **Pods** tab page, verify that the new pod and the existing backend pod run on the same node.

Using YAML
----------

-  Workload affinity

   Kubernetes supports affinity between pods, which allows the frontend and backend pods of an application to be deployed together to minimize access latency.

   Assume that the backend pods of an application have been created with label **app=backend**. You can use **.spec.affinity.podAffinity** to configure workload affinity so that the frontend pods (labeled **app=frontend**) and backend pods (labeled **app=backend**) can be deployed together.

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: frontend
        labels:
          app: frontend
      spec:
        selector:
          matchLabels:
            app: frontend
        replicas: 3
        template:
          metadata:
            labels:
              app: frontend
          spec:
            containers:
            - image: nginx:alpine
              name: frontend
              resources:
                requests:
                  cpu: 100m
                  memory: 200Mi
                limits:
                  cpu: 100m
                  memory: 200Mi
            imagePullSecrets:
            - name: default-secret
            affinity:  # Configure a scheduling policy.
              podAffinity:  # Workload affinity scheduling rule
                requiredDuringSchedulingIgnoredDuringExecution:   # Scheduling policy that must be met
                - topologyKey: prefer    # Topology keys are divided based on node labels, among which prefer is a custom label.
                  labelSelector:  # Select workloads that meet the requirements based on workload labels.
                    matchExpressions: # Workload label matching rule
                    - key: app # The key of the workload label is app.
                      operator: In # The rule is met if a value exists in the value list.
                      values: # Workload label values
                      - backend
                preferredDuringSchedulingIgnoredDuringExecution:    # Scheduling policy that is met as much as possible
                - weight: 100  # Priority that can be configured when the best-effort policy is used. The value ranges from 1 to 100. A larger value indicates a higher priority.
                  podAffinityTerm:  # Affinity configuration when the best-effort policy is used
                    topologyKey: topology.kubernetes.io/zone   # Topology keys are divided based on node labels by node AZ.
                    labelSelector:
                      matchExpressions:
                      - key: app
                        operator: In
                        values:
                        - backend

   During workload scheduling in the preceding example, node topology keys are divided based on the **prefer** label using the rule that must be met. If backend pods (labeled **app=backend**) are running on a node in the topology key, frontend pods (labeled **app=frontend**) will also be deployed in that topology key, even if not all nodes in the topology key are running the backend pods. According to the best-effort rule, topology keys are divided based on **topology.kubernetes.io/zone** by node AZ. This ensures that the frontend and backend pods are deployed on nodes within the same AZ as much as possible.

   .. note::

      For workload affinity, **topologyKey** cannot be left blank when **requiredDuringSchedulingIgnoredDuringExecution** and **preferredDuringSchedulingIgnoredDuringExecution** are used.

      **topologyKey** is used to divide topology keys based on the labels of nodes. Nodes with the same labels are grouped into the same topology key. The scheduler then selects the topology key to be scheduled based on the workload label. A topology key can consist of multiple nodes. If a workload that meets a label selection rule runs on a node in a topology key, all nodes in the topology key can be scheduled.

      For example, if the **topologyKey** label is set to **topology.kubernetes.io/zone**, nodes' AZs will be used as the topology keys, and workloads will be scheduled by AZ during deployment.

-  Workload anti-affinity

   In some cases, pods need to be deployed separately. This is because deploying them together can negatively impact performance.

   Assume that the frontend pods of an application have been created with label **app=frontend**. To ensure that pods are deployed on different nodes and multiple AZs are preferred, you can use **.spec.affinity.podAntiAffinity** to configure workload anti-affinity.

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name:   frontend
        labels:
          app:  frontend
      spec:
        selector:
          matchLabels:
            app: frontend
        replicas: 5
        template:
          metadata:
            labels:
              app:  frontend
          spec:
            containers:
            - image:  nginx:alpine
              name:  frontend
              resources:
                requests:
                  cpu:  100m
                  memory:  200Mi
                limits:
                  cpu:  100m
                  memory:  200Mi
            imagePullSecrets:
            - name: default-secret
            affinity:
              podAntiAffinity:  # Workload anti-affinity scheduling rule
                requiredDuringSchedulingIgnoredDuringExecution:   # Scheduling policy that must be met
                - topologyKey: kubernetes.io/hostname    # Topology keys are divided based on node labels.
                  labelSelector:    # Pod label matching rule
                    matchExpressions:  # The key of the workload label is app.
                    - key: app  # The key of the workload label is app.
                      operator: In  # The rule is met if a value exists in the value list.
                      values:  # Workload label values
                      - frontend
                preferredDuringSchedulingIgnoredDuringExecution:    # Scheduling policy that is met as much as possible
                - weight: 100  # Priority that can be configured when the best-effort policy is used. The value ranges from 1 to 100. A larger value indicates a higher priority.
                  podAffinityTerm:  # Affinity configuration when the best-effort policy is used
                    topologyKey: topology.kubernetes.io/zone   # Topology keys are divided based on node labels.
                    labelSelector:
                      matchExpressions:
                      - key: app
                        operator: In
                        values:
                        - frontend

   In the preceding example, anti-affinity rules are configured. The rule that must be met indicates that node topology keys are divided based on **kubernetes.io/hostname**. Nodes with the **kubernetes.io/hostname** label have different label values. Therefore, there is only one node in each topology key. If a topology key contains only one node where a **frontend** pod already exists, pods with the same label will not be scheduled to that topology key. According to the best-effort rule, topology keys are divided based on **topology.kubernetes.io/zone** by node AZ. This ensures that the pods are deployed on nodes in different AZs as much as possible.

   .. note::

      For workload anti-affinity, when **requiredDuringSchedulingIgnoredDuringExecution** is used, the default access controller **LimitPodHardAntiAffinityTopology** of Kubernetes requires that **topologyKey** can only be **kubernetes.io/hostname**. To use other custom topology logic, modify or disable the access controller.

.. |image1| image:: /_static/images/en-us_image_0000002065638630.png
