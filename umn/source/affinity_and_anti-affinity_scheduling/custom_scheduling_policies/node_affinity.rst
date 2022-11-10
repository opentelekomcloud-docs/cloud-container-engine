:original_name: cce_01_0232.html

.. _cce_01_0232:

Node Affinity
=============

Using the CCE Console
---------------------

#. Log in to the CCE console and choose **Workloads** > **Deployments** or **Workloads** > **StatefulSets** in the navigation pane.

#. Click a workload name in the Deployment or StatefulSet list. On the displayed workload details page, click the **Scheduling Policies** tab and then click **Add Custom Scheduling Policy**.

#. In the **Node Affinity** area, you can specify node labels to meet required or preferred rules in scheduling.

   .. table:: **Table 1** Node affinity settings

      +-----------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter | Description                                                                                                                                                                                                                                                                                                                                          |
      +===========+======================================================================================================================================================================================================================================================================================================================================================+
      | Required  | It specifies a rule that must be met in scheduling. It corresponds to **requiredDuringSchedulingIgnoredDuringExecution** in Kubernetes. You can click **Add Rule** to add multiple required rules. A pod will be scheduled on a node that meets any of the rules configured.                                                                         |
      +-----------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Preferred | It specifies a preference in scheduling. It corresponds to **preferredDuringSchedulingIgnoredDuringExecution** in Kubernetes. You can click **Add Rule** to add multiple preferred rules. The scheduler will try to enforce the rules but will not guarantee. If the scheduler cannot satisfy any one of the rules, the pod will still be scheduled. |
      +-----------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Set a rule according to the following table. You can click **Add Selector** to configure multiple selectors for a rule.

   .. table:: **Table 2** Selector settings

      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                  |
      +===================================+==============================================================================================================================================================================================================================================================================================================================================================+
      | Weight                            | -  This parameter is unavailable for a required rule.                                                                                                                                                                                                                                                                                                        |
      |                                   | -  Set the weight of a preferred rule. A higher weight indicates a higher priority.                                                                                                                                                                                                                                                                          |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Label                             | Node label. You can use the default label or customize a label.                                                                                                                                                                                                                                                                                              |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Operator                          | The following relations are supported: **In**, **NotIn**, **Exists**, **DoesNotExist**, **Gt**, and **Lt**                                                                                                                                                                                                                                                   |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Value                             | Tag value.                                                                                                                                                                                                                                                                                                                                                   |
      |                                   |                                                                                                                                                                                                                                                                                                                                                              |
      |                                   | Operators **In** and **NotIn** allow one or more label values. Values are separated with colons (;). Operators **Exists** and **DoesNotExist** are used to determine whether a label exists, and do not require a label value. If you set the operator to **Gt** or **Lt** for a label, the label value must be greater than or less than a certain integer. |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Operation                         | You can click **Delete** to delete a selector.                                                                                                                                                                                                                                                                                                               |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Add Selector                      | A selector corresponds to **matchExpressions** in Kubernetes. You can click **Add Selector** to add multiple selectors for a scheduling rule. The rule is applied in scheduling only when all its selectors are satisfied.                                                                                                                                   |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+


   .. figure:: /_static/images/en-us_image_0000001190658439.png
      :alt: **Figure 1** Node affinity scheduling policy

      **Figure 1** Node affinity scheduling policy

Using kubectl
-------------

This section uses Nginx as an example to describe how to configure node affinity.

**Prerequisites**

A workload that uses the nginx container image has been deployed on a node.

**Procedure**

Set **Label** to **kubernetes.io/hostname**, add affinity nodes, and set the operator to **In**. Then, click **OK**.

YAML file of the workload with node affinity:

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
           nodeAffinity:
             requiredDuringSchedulingIgnoredDuringExecution:
               nodeSelectorTerms:
                 - matchExpressions:
                     - key: kubernetes.io/hostname
                       operator: In
                       values:
                        - 192.168.6.174
