:original_name: cce_10_0566.html

.. _cce_10_0566:

New Pod Check
=============

Check Item
----------

-  Check whether pods can be created on the existing nodes after the cluster is upgraded.
-  Check whether pods can be created on new nodes after the cluster is upgraded.

Procedure
---------

After creating a node based on :ref:`New Node Check <cce_10_0565>`, create a DaemonSet workload to create pods on each node.

Go to the CCE console, access the cluster console, and choose **Workloads** in the navigation pane. On the displayed page, switch to the **DaemonSets** tab page and click **Create Workload** or **Create from YAML** in the upper right corner.

You are advised to use the image for routine tests as the base image. You can deploy a pod by referring to the following YAML file.

.. note::

   In this test, YAML deploys DaemonSet in the default namespace, uses **ngxin:perl** as the base image, requests 10 MB CPU and 10 Mi memory, and limits 100 MB CPU and 50 Mi memory.

.. code-block::

   apiVersion: apps/v1
   kind: DaemonSet
   metadata:
     name: post-upgrade-check
     namespace: default
   spec:
     selector:
       matchLabels:
         app: post-upgrade-check
         version: v1
     template:
       metadata:
         labels:
           app: post-upgrade-check
           version: v1
       spec:
         containers:
           - name: container-1
             image: nginx:perl
             imagePullPolicy: IfNotPresent
             resources:
               requests:
                 cpu: 10m
                 memory: 10Mi
               limits:
                 cpu: 100m
                 memory: 50Mi

After the workload is created, check whether the pod status of the workload is normal.

After the check is complete, go to the CCE console and access the cluster console. Choose **Workloads** in the navigation pane. On the displayed page, switch to the **DaemonSets** tab page, choose **More** > **Delete** in the **Operation** column of the **post-upgrade-check** workload to delete the test workload.

Solution
--------

If the pod cannot be created or the pod status is abnormal, contact technical support and specify whether the exception occurs on new nodes or existing nodes.
