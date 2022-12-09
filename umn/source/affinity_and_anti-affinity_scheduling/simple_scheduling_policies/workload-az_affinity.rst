:original_name: cce_01_0228.html

.. _cce_01_0228:

Workload-AZ Affinity
====================

Using the CCE Console
---------------------

#. When :ref:`Creating a Deployment <cce_01_0047>` or :ref:`Creating a StatefulSet <cce_01_0048>`, in the **Scheduling Policies** area on the **Configure Advanced Settings** page, click |image1| next to **Workload-AZ Affinity and Anti-affinity** > **Affinity with AZs**.

#. Select the AZ in which you want to deploy the workload.

   The created workload will be deployed in the selected AZ.

.. _cce_01_0228__section4201420133117:

Using kubectl
-------------

This section uses an Nginx workload as an example to describe how to create a workload using kubectl.

**Prerequisites**

The ECS where the kubectl client runs has been connected to your cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_01_0107>`.

**Procedure**

When :ref:`using kubectl to create a Deployment <cce_01_0047__section155246177178>` or :ref:`using kubectl to create a StatefulSet <cce_01_0048__section113441881214>`, configure workload-AZ affinity. The following is an example YAML file for workload-AZ affinity.

.. code-block::

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: az-in-deployment
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: az-in-deployment
     strategy:
       type: RollingUpdate
     template:
       metadata:
         labels:
           app: az-in-deployment
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
                 - key: failure-domain.beta.kubernetes.io/zone #node's label key
                   operator: In
                   values:
                   - az1                              #node's key value

Setting the Object Type After Creating a Workload
-------------------------------------------------

#. Log in to the CCE console and choose **Workloads** > **Deployments** or **Workloads** > **StatefulSets** in the navigation pane.
#. Click the name of the workload for which you will add a scheduling policy. On the workload details page, choose **Scheduling Policies** > **Add Simple Scheduling Policy** > **Add Affinity Object**.
#. Set **Object Type** to **Availability Zone**, and select the AZ in which the workload is eligible to be deployed. The workload will be deployed in the selected AZ.

   .. note::

      This method can be used to add, edit, or delete scheduling policies.

.. |image1| image:: /_static/images/en-us_image_0198873490.png
