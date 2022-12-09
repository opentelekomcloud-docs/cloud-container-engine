:original_name: cce_01_0234.html

.. _cce_01_0234:

Workload Anti-Affinity
======================

Using the CCE Console
---------------------

Workload anti-affinity determines the pods from which the target workload will be deployed in a different topology domain.

#. Log in to the CCE console and choose **Workloads** > **Deployments** or **Workloads** > **StatefulSets** in the navigation pane.

#. Click a workload name in the Deployment or StatefulSet list. On the displayed workload details page, click the **Scheduling Policies** tab and then click **Add Custom Scheduling Policy**.

#. In the **Pod Anti-Affinity** area, set the namespace, topology key, and the label requirements to be met.

   There are two types of pod anti-affinity rules: **Required** (hard rule) and **Preferred** (soft rule), and the label operators include **In**, **NotIn**, **Exists**, and **DoesNotExist**.

   .. table:: **Table 1** Workload anti-affinity settings

      +-----------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter | Description                                                                                                                                                                                                                                                                                                                    |
      +===========+================================================================================================================================================================================================================================================================================================================================+
      | Required  | It specifies a rule that must be met in scheduling. It corresponds to **requiredDuringSchedulingIgnoredDuringExecution** in Kubernetes. You can add multiple required rules. Ensure that all the labels specified in the rules must be in the same workload. Each rule requires a namespace and topology key.                  |
      +-----------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Preferred | It specifies a preference in scheduling. It corresponds to **preferredDuringSchedulingIgnoredDuringExecution** in Kubernetes. You can add multiple preferred rules. The scheduler will try to enforce the rules but will not guarantee. If the scheduler cannot satisfy any one of the rules, the pod will still be scheduled. |
      +-----------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

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


   .. figure:: /_static/images/en-us_image_0000001144738550.png
      :alt: **Figure 1** Pod anti-affinity scheduling policy

      **Figure 1** Pod anti-affinity scheduling policy

Using kubectl
-------------

This section uses Nginx as an example to describe how to configure pod anti-affinity.

**Prerequisites**

A workload that uses the nginx container image has been deployed on a node.

**Procedure**

Set **Namespace** to **default** and **Topology Key** to the built-in node label **kubernetes.io/hostname**, which means that the scheduling scope is a node. Set the label **app** and its value to **redis**. Set **Operator** to **In** and click **OK**.

The YAML of the workload with pod anti-affinity:

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
           podAffinity: {}
           podAntiAffinity:
             requiredDuringSchedulingIgnoredDuringExecution:
               - labelSelector:
                   matchExpressions:
                     - key: app
                       operator: In
                       values:
                         - redis
                 namespaces:
                   - default
                 topologyKey: kubernetes.io/hostname
