:original_name: cce_01_0229.html

.. _cce_01_0229:

Workload-AZ Anti-Affinity
=========================

Using the CCE Console
---------------------

#. When :ref:`Creating a Deployment <cce_01_0047>` or :ref:`Creating a StatefulSet <cce_01_0048>`, in the **Scheduling Policies** area on the **Configure Advanced Settings** page, click |image1| next to **Workload-AZ Affinity and Anti-affinity** > **Anti-affinity with AZs**.

#. Select an AZ in which the workload is ineligible to be deployed.

   The created workload is not deployed on the selected AZ.

.. _cce_01_0229__section102822029173111:

Using kubectl
-------------

This section uses Nginx as an example to describe how to create a workload using kubectl.

**Prerequisites**

The ECS where the kubectl client runs has been connected to your cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_01_0107>`.

**Procedure**

When :ref:`using kubectl to create a Deployment <cce_01_0047__section155246177178>` or :ref:`using kubectl to create a StatefulSet <cce_01_0048__section113441881214>`, configure workload-AZ anti-affinity. The following is an example YAML file for workload-AZ anti-affinity.

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
                 - key: failure-domain.beta.kubernetes.io/zone       #node's label key
                   operator: NotIn
                   values:
                   - az1                                   #node's key value

Setting the Object Type After Creating a Workload
-------------------------------------------------

#. Log in to the CCE console and choose **Workloads** > **Deployments** or **Workloads** > **StatefulSets** in the navigation pane.
#. Click the name of the workload for which you will add a scheduling policy. On the workload details page, choose **Scheduling Policies** > **Add Simple Scheduling Policy** > **Add Anti-affinity Object**.
#. Set **Object Type** to **Availability Zone** and select the AZ in which the workload is ineligible to be deployed. The workload will be constrained from being deployed in the selected AZ.

   .. note::

      This method can be used to add, edit, or delete scheduling policies.

.. |image1| image:: /_static/images/en-us_image_0198876479.png
