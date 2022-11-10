:original_name: cce_01_0220.html

.. _cce_01_0220:

Workload-Workload Affinity
==========================

Using the CCE Console
---------------------

#. When :ref:`Creating a Deployment <cce_01_0047>` or :ref:`Creating a StatefulSet <cce_01_0048>`, in the **Scheduling Policies** area on the **Configure Advanced Settings** page, choose **Inter-Pod Affinity and Anti-affinity** > **Affinity with Pods** > **Add**.

#. Select the workloads that will be co-located with the current workload on the same node, and click **OK**.

   The workload to be created will be deployed on the same node as the selected affinity workloads.

.. _cce_01_0220__section5140193643912:

Using kubectl
-------------

This section uses Nginx as an example to describe how to create a workload using kubectl.

**Prerequisites**

The ECS where the kubectl client runs has been connected to your cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_01_0107>`.

**Procedure**

When :ref:`using kubectl to create a Deployment <cce_01_0047__section155246177178>` or :ref:`using kubectl to create a StatefulSet <cce_01_0048__section113441881214>`, configure workload-workload affinity. The following is an example YAML file for workload-workload affinity.

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
           podAffinity:
             requiredDuringSchedulingIgnoredDuringExecution:
               nodeSelectorTerms:
               - matchExpressions:
                 - key: app          #workload's label key
                   operator: In
                   values:
                   - test     #workload's label value

Setting the Object Type After Creating a Workload
-------------------------------------------------

#. Log in to the CCE console and choose **Workloads** > **Deployments** or **Workloads** > **StatefulSets** in the navigation pane.
#. Click the name of the workload for which you will add a scheduling policy. On the workload details page, choose **Scheduling Policies** > **Add Simple Scheduling Policy** > **Add Affinity Object**.
#. Set **Object Type** to **Workload** and select the workloads to be deployed on the same node as the created workload. The created workload and the selected workloads will be deployed on the same node.

   .. note::

      This method can be used to add, edit, or delete scheduling policies.
