:original_name: cce_01_0226.html

.. _cce_01_0226:

Workload-Node Anti-Affinity
===========================

Using the CCE Console
---------------------

#. When :ref:`Creating a Deployment <cce_01_0047>` or :ref:`Creating a StatefulSet <cce_01_0048>`, in the **Scheduling Policies** area on the **Configure Advanced Settings** page, choose **Workload-Node Affinity and Anti-affinity** > **Anti-affinity with Nodes** > **Add**.

#. Select the node on which the workload is ineligible to be deployed, and click **OK**.

   If you select multiple nodes, the workload will not be deployed on these nodes.

.. _cce_01_0226__section1361482522712:

Using kubectl
-------------

This section uses Nginx as an example to describe how to create a workload using kubectl.

**Prerequisites**

The ECS where the kubectl client runs has been connected to your cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_01_0107>`.

**Procedure**

When :ref:`using kubectl to create a Deployment <cce_01_0047__section155246177178>` or :ref:`using kubectl to create a StatefulSet <cce_01_0048__section113441881214>`, configure workload-node affinity. The following is an example YAML file for workload-node anti-affinity.

.. code-block::

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: nginx
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: nginx
     strategy:
       type: RollingUpdate
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
                 - key: nodeName          #node's label key
                   operator: NotIn        #Indicates that the workload will not be deployed on the node.
                   values:
                   - test-node-1          #node's label value

Setting the Object Type After Creating a Workload
-------------------------------------------------

#. Log in to the CCE console and choose **Workloads** > **Deployments** or **Workloads** > **StatefulSets** in the navigation pane.
#. Click the name of the workload for which you will add a scheduling policy. On the workload details page, choose **Scheduling Policies** > **Add Simple Scheduling Policy** > **Add Anti-affinity Object**.
#. Set **Object Type** to **Node** and select the node on which the workload is ineligible to be deployed. The workload will be constrained from being deployed on the selected node.

   .. note::

      This method can be used to add, edit, or delete scheduling policies.
