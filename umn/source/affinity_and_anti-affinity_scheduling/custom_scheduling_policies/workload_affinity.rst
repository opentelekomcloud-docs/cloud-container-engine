:original_name: cce_01_0233.html

.. _cce_01_0233:

Workload Affinity
=================

Using the CCE Console
---------------------

Workload affinity determines the pods as which the target workload will be deployed in the same topology domain.

#. Log in to the CCE console and choose **Workloads** > **Deployments** or **Workloads** > **StatefulSets** in the navigation pane.

#. Click a workload name in the Deployment or StatefulSet list. On the displayed workload details page, click the **Scheduling Policies** tab and then click **Add Custom Scheduling Policy**.

#. In the **Pod Affinity** area, set the namespace, topology key, and the label requirements to be met.

   There are two types of pod affinity rules: **Required** (hard rule) and **Preferred** (soft rule). The label operators include **In**, **NotIn**, **Exists**, and **DoesNotExist**.

   .. table:: **Table 1** Pod affinity settings

      +-----------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter | Description                                                                                                                                                                                                                                                                                                                                          |
      +===========+======================================================================================================================================================================================================================================================================================================================================================+
      | Required  | It specifies a rule that must be met in scheduling. It corresponds to **requiredDuringSchedulingIgnoredDuringExecution** in Kubernetes. You can click **Add Rule** to add multiple required rules. Ensure that all the labels specified in the rules must be in the same workload. Each rule requires a namespace and topology key.                  |
      +-----------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Preferred | It specifies a preference in scheduling. It corresponds to **preferredDuringSchedulingIgnoredDuringExecution** in Kubernetes. You can click **Add Rule** to add multiple preferred rules. The scheduler will try to enforce the rules but will not guarantee. If the scheduler cannot satisfy any one of the rules, the pod will still be scheduled. |
      +-----------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Set a rule according to the following table. You can click **Add Selector** to configure multiple selectors for a rule.

   .. table:: **Table 2** Selector settings

      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                    |
      +===================================+================================================================================================================================================================================================================================+
      | Weight                            | -  This parameter is unavailable for a required rule.                                                                                                                                                                          |
      |                                   | -  Set the weight of a preferred rule. A higher weight indicates a higher priority.                                                                                                                                            |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Namespace                         | By default, the namespace of the current pod is used. You can also use another namespace.                                                                                                                                      |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Topology Key                      | Key of the worker node label that the system uses to denote a topology domain in which scheduling can be performed. Default and custom node labels can be used.                                                                |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Label                             | Label of the workload. You can customize the label name.                                                                                                                                                                       |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Operator                          | The following relations are supported: **In**, **NotIn**, **Exists**, and **DoesNotExist**                                                                                                                                     |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Value                             | Tag value.                                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                |
      |                                   | Operators **In** and **NotIn** allow one or more label values. Values are separated with colons (;). Operators **Exists** and **DoesNotExist** are used to determine whether a label exists, and do not require a label value. |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Operation                         | You can click **Delete** to delete a selector.                                                                                                                                                                                 |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Add Selector                      | A selector corresponds to **matchExpressions** in Kubernetes. You can click **Add Selector** to add multiple selectors for a scheduling rule. The rule is applied in scheduling only when all its selectors are satisfied.     |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+


   .. figure:: /_static/images/en-us_image_0000001144578756.png
      :alt: **Figure 1** Pod affinity scheduling policy

      **Figure 1** Pod affinity scheduling policy

Using kubectl
-------------

This section uses Nginx as an example to describe how to configure pod affinity.

**Prerequisites**

A workload that uses the nginx container image has been deployed on a node.

**Procedure**

Set **Namespace** to **default** and **Topology Key** to the built-in node label **kubernetes.io/hostname**, which means that the scheduling scope is a node. Set labels **app** and **type** and their value to **redis** and **database**, respectively. Set **Operator** to **In** and click **OK**.

The YAML of the workload with pod affinity is as follows:

.. code-block::

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: nginx
     namespace: default
   spec:
     replicas: 2
     selector:
       matchLabels:
         app: nginx
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
         - image: nginx
           imagePullPolicy: Always
           name: nginx
         imagePullSecrets:
         - name: default-secret
         affinity:
           nodeAffinity: {}
           podAffinity:
             requiredDuringSchedulingIgnoredDuringExecution:
               - labelSelector:
                   matchExpressions:
                     - key: app
                       operator: In
                       values:
                         - redis
                     - key: type
                       operator: In
                       values:
                         - database
                 namespaces:
                   - default
                 topologyKey: kubernetes.io/hostname

.. important::

   In this example, only when a candidate workload (for example, workload A) with both labels **app=redis** and **type=database** is found can the workload Nginx be successfully scheduled to the node of the candidate workload.
