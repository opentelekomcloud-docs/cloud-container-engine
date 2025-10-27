:original_name: cce_10_0892.html

.. _cce_10_0892:

Configuring Node Affinity Scheduling (nodeAffinity)
===================================================

Kubernetes can schedule workload pods to affinity nodes based on their labels and label values. For example, some nodes support GPU computing, and node affinity scheduling can guarantee that high-performance computing pods run on these GPU nodes.

Using the CCE Console
---------------------

In this example, a GPU node labeled with **gpu=true** has been created in the cluster. You can use this label to schedule pods to this GPU node.

#. Log in to the CCE console.

#. Click the cluster name to go to the cluster console, choose **Workloads** in the navigation pane, and click the **Create Workload** in the upper right corner.

#. In **Advanced Settings**, choose **Scheduling** and select a policy for **Node Affinity**. In this example, **Customize affinity** is selected. For details about how to create a workload, see :ref:`Creating a Workload <cce_10_0673>`.

   .. table:: **Table 1** Scheduling policies

      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Parameter             | Description                                                                                                                                                                                                                                                                                                                                  | Example               |
      +=======================+==============================================================================================================================================================================================================================================================================================================================================+=======================+
      | Node Affinity         | -  **Not configured**: No node affinity policy is configured.                                                                                                                                                                                                                                                                                | Customize affinity    |
      |                       | -  **Specify node**: Specify the nodes where workload pods are to be deployed. If no nodes are specified, the pods will be randomly scheduled according to the default cluster scheduling policy.                                                                                                                                            |                       |
      |                       | -  **Specify node pool**: Specify the node pools where workload pods are to be deployed. If no node pools are specified, the pods will be randomly scheduled according to the default cluster scheduling policy.                                                                                                                             |                       |
      |                       | -  **Customize affinity**: allow flexible scheduling of workload pods based on node labels. For details about the supported affinity policies, see :ref:`Table 2 <cce_10_0892__table19928132891314>`. Select a proper policy type and add a policy. For details about the parameters, see :ref:`Table 3 <cce_10_0892__table12814123831611>`. |                       |
      +-----------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+

#. Select a proper node affinity rule and click |image1| to add a scheduling policy. In this example, a scheduling policy is added in the **Required** area under **Node Affinity**. This policy specifies that the newly created workload can only be scheduled to a specific node.

   .. _cce_10_0892__table19928132891314:

   .. table:: **Table 2** Affinity rule

      +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Parameter             | Description                                                                                                                                    | Example               |
      +=======================+================================================================================================================================================+=======================+
      | Node Affinity         | -  **Required**: indicates the hard constraint (**requiredDuringSchedulingIgnoredDuringExecution**) that must be met.                          | Required              |
      |                       |                                                                                                                                                |                       |
      |                       |    If multiple rules that must be met are added, scheduling will be performed when only one rule is met.                                       |                       |
      |                       |                                                                                                                                                |                       |
      |                       | -  **Preferred**: indicates the soft constraint (**preferredDuringSchedulingIgnoredDuringExecution**) that need to be met as much as possible. |                       |
      |                       |                                                                                                                                                |                       |
      |                       |    If multiple rules that are preferentially met are added, scheduling will be performed even if one or none of the rules is met.              |                       |
      +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+

#. In the dialog box that is displayed on the right, click **Add Policy** to configure rules for filtering node labels.

   You can also click **Specify Node** or **Specify AZ** to quickly select a node or AZ on the console for scheduling.

   Specifying a node or AZ is also implemented through labels. The console frees you from manually entering node labels. The **kubernetes.io/hostname** label is used when you specify a node, and the **failure-domain.beta.kubernetes.io/zone** label is used when you specify an AZ.

   .. _cce_10_0892__table12814123831611:

   .. table:: **Table 3** Parameters for configuring node affinity scheduling policies

      +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Parameter             | Description                                                                                                                                                                                                                                                                                                                                          | Example               |
      +=======================+======================================================================================================================================================================================================================================================================================================================================================+=======================+
      | Weight                | This parameter is available only in a **preferred** scheduling policy. Weights range from 1 to 100 and are taken into account as an extra scoring factor during scheduling. The scheduler combines the weight with other priority functions of the node to determine the final score and then assigns pods to the node with the highest total score. | None                  |
      +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Label Key             | When configuring node affinity, enter the node label to be matched.                                                                                                                                                                                                                                                                                  | gpu                   |
      |                       |                                                                                                                                                                                                                                                                                                                                                      |                       |
      |                       | Both default labels and custom labels are supported. For details about default node labels, see :ref:`Inherent Label of a Node <cce_10_0004__section74111324152813>`.                                                                                                                                                                                |                       |
      +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Operator              | The following operators are supported:                                                                                                                                                                                                                                                                                                               | In                    |
      |                       |                                                                                                                                                                                                                                                                                                                                                      |                       |
      |                       | -  **In**: The label of the affinity or anti-affinity object is in the label value list (**values** field).                                                                                                                                                                                                                                          |                       |
      |                       | -  **NotIn**: The label of the affinity or anti-affinity object is not in the label value list (**values** field).                                                                                                                                                                                                                                   |                       |
      |                       | -  **Exists**: The affinity or anti-affinity object has a specified label key.                                                                                                                                                                                                                                                                       |                       |
      |                       | -  **DoesNotExist**: The affinity or anti-affinity object does not have a specified label key.                                                                                                                                                                                                                                                       |                       |
      |                       | -  **Gt**: The label value of the scheduling node is greater than what is listed (character string comparison).                                                                                                                                                                                                                                      |                       |
      |                       | -  **Lt**: The label value of the scheduling node is less than what is listed (character string comparison).                                                                                                                                                                                                                                         |                       |
      +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+
      | Label Value           | When configuring node affinity, enter the value of the node label.                                                                                                                                                                                                                                                                                   | true                  |
      +-----------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------+

#. After the scheduling policy is added, click **Create Workload**.

#. Verify that all pods run on the target node.

   a. In the navigation pane, choose **Workloads**.
   b. Click the workload name to enter its details page. On the **Pods** tab page, verify that all pods run on the target node, which is labeled with **gpu=true**.

Using YAML
----------

Workload node affinity rules are implemented using node labels. When a node is created in a CCE cluster, it is automatically assigned certain labels. Here are some examples of commonly used node labels (for more labels, see :ref:`Inherent Label of a Node <cce_10_0004__section74111324152813>`):

-  **topology.kubernetes.io/zone**: the AZ where the node is located, which can be used for scheduling in a specified AZ.
-  **kubernetes.io/hostname**: hostname of a node, which can be used for specified node scheduling.
-  **cce.cloud.com/cce-nodepool**: node pool to which a node belongs, which can be used for scheduling in a specified node pool.

In this example, the rule that **must be met** indicates that the node to be scheduled must be labeled with key **gpu** and value **true**. The rule that needs to be **met as much as possible** indicates that pods are preferentially scheduled to nodes in AZ 1 based on **topology.kubernetes.io/zone**. The following is an example of configuring node affinity:

.. code-block::

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name:  gpu
     labels:
       app:  gpu
   spec:
     selector:
       matchLabels:
         app: gpu
     replicas: 3
     template:
       metadata:
         labels:
           app:  gpu
       spec:
         containers:
         - image:  nginx:alpine
           name:  gpu
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
           nodeAffinity:  # Node affinity scheduling
             requiredDuringSchedulingIgnoredDuringExecution:  # Scheduling policy that must be met
               nodeSelectorTerms:     # Select a node that meets the requirements according to the node label.
                 - matchExpressions:    # Node label matching rule
                   - key: gpu   # The key of the node label is gpu.
                     operator: In  # The rule is met if a value exists in the value list.
                     values:   # The value of the node label is true.
                     - "true"
             preferredDuringSchedulingIgnoredDuringExecution:  # Scheduling policy that is met as much as possible
               - weight: 100  # Priority that can be configured when the best-effort policy is used. The value ranges from 1 to 100. A larger value indicates a higher priority.
                 preference:  # Preferred node label matching rule when the best-effort policy is used
                   matchExpressions:   # Node label matching rule
                     - key: topology.kubernetes.io/zone   # Nodes' AZs
                        operator: In  # The rule is met if a value exists in the value list.
                       values:   # The value of the node label is az1.
                       - "az1"

.. note::

   There is no anti-affinity policy for node affinity scheduling. For node anti-affinity, you can filter nodes by label using the **NotIn** and **DoesNotExist** operators.

.. |image1| image:: /_static/images/en-us_image_0000002434080996.png
