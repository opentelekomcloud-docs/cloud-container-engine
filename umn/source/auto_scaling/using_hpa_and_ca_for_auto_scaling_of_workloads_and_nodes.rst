:original_name: cce_10_0300.html

.. _cce_10_0300:

Using HPA and CA for Auto Scaling of Workloads and Nodes
========================================================

Application Scenarios
---------------------

The best way to handle surging traffic is to automatically adjust the number of machines based on the traffic volume or resource usage, which is called scaling.

When pods or containers are used for deploying applications, the upper limit of available resources is typically required to set for pods or containers to prevent unlimited usage of node resources during peak hours. However, after the upper limit is reached, an application error may occur. To resolve this issue, scale in the number of pods to share workloads. If the node resource usage increases to a certain extent that newly added pods cannot be scheduled, scale in the number of nodes based on the node resource usage.

Solution
--------

Two major auto scaling policies are HPA (Horizontal Pod Autoscaling) and CA (Cluster AutoScaling). HPA is for workload auto scaling and CA is for node auto scaling.

HPA and CA work with each other. HPA requires sufficient cluster resources for successful scaling. When the cluster resources are insufficient, CA is needed to add nodes. If HPA reduces workloads, the cluster will have a large number of idle resources. In this case, CA needs to release nodes to avoid resource waste.

As shown in :ref:`Figure 1 <cce_10_0300__cce_bestpractice_00282_fig6540132372015>`, HPA performs scale-out based on the monitoring metrics. When cluster resources are insufficient, newly created pods are in Pending state. CA then checks these pending pods and selects the most appropriate node pool based on the configured scaling policy to scale out the node pool.

.. _cce_10_0300__cce_bestpractice_00282_fig6540132372015:

.. figure:: /_static/images/en-us_image_0000001851744000.png
   :alt: **Figure 1** HPA and CA working flows

   **Figure 1** HPA and CA working flows

Using HPA and CA can easily implement auto scaling in most scenarios. In addition, the scaling process of nodes and pods can be easily observed.

This section uses an example to describe the auto scaling process using HPA and CA policies together.

Preparations
------------

#. Create a cluster with one node. The node should have 2 cores of vCPUs and 4 GiB of memory, or a higher specification, as well as an EIP to allow external access. If no EIP is bound to the node during node creation, you can manually bind one on the ECS console after creating the node.
#. Install add-ons for the cluster.

   -  autoscaler: node scaling add-on
   -  metrics-server: an aggregator of resource usage data in a Kubernetes cluster. It can collect measurement data of major Kubernetes resources, such as pods, nodes, containers, and Services.

#. Log in to the cluster node and run a computing-intensive application. When a user sends a request, the result needs to be calculated before being returned to the user.

   a. Create a PHP file named **index.php** to calculate the square root of the request for 1,000,000 times before returning **OK!**.

      .. code-block::

         vi index.php

      The file content is as follows:

      .. code-block::

         <?php
           $x = 0.0001;
           for ($i = 0; $i <= 1000000; $i++) {
             $x += sqrt($x);
           }
           echo "OK!";
         ?>

   b. Compile a **Dockerfile** file to build an image.

      .. code-block::

         vi Dockerfile

      The content is as follows:

      .. code-block::

         FROM php:5-apache
         COPY index.php /var/www/html/index.php
         RUN chmod a+rx index.php

   c. Run the following command to build an image named **hpa-example** with the tag **latest**.

      .. code-block::

         docker build -t hpa-example:latest .

   d. .. _cce_10_0300__cce_bestpractice_00282_li108181514125:

      (Optional) Log in to the SWR console, choose **Organizations** in the navigation pane, and click **Create Organization** in the upper right corner to create an organization.

      Skip this step if you already have an organization.

   e. .. _cce_10_0300__cce_bestpractice_00282_li187221141362:

      In the navigation pane, choose **My Images** and then click **Upload Through Client**. On the page displayed, click **Generate a temporary login command** and click |image1| to copy the command.

   f. Run the login command copied in the previous step on the cluster node. If the login is successful, the message "Login Succeeded" is displayed.

   g. Tag the hpa-example image.

      **docker tag** *{Image name 1*:*Tag 1}*/*{Image repository address}*/*{Organization name}*/*{Image name 2*:*Tag 2}*

      -  *{Image name 1:Tag 1}*: name and tag of the local image to be uploaded.
      -  *{Image repository address}*: the domain name at the end of the login command in :ref:`login command <cce_10_0300__cce_bestpractice_00282_li187221141362>`. It can be obtained on the SWR console.
      -  *{Organization name}*: name of the :ref:`created organization <cce_10_0300__cce_bestpractice_00282_li108181514125>`.
      -  *{Image name 2:Tag 2}*: desired image name and tag to be displayed on the SWR console.

      The following is an example:

      **docker tag hpa-example:latest swr.eu-de.otc.t-systems.com/group/hpa-example:latest**

   h. Push the image to the image repository.

      **docker push** *{Image repository address}*/*{Organization name}*/*{Image name 2:Tag 2}*

      The following is an example:

      **docker push swr.eu-de.otc.t-systems.com/group/hpa-example:latest**

      The following information will be returned upon a successful push:

      .. code-block::

         6d6b9812c8ae: Pushed
         ...
         fe4c16cbf7a4: Pushed
         latest: digest: sha256:eb7e3bbd*** size: **

      To view the pushed image, go to the SWR console and refresh the **My Images** page.

Creating a Node Pool and a Node Scaling Policy
----------------------------------------------

#. Log in to the CCE console, access the created cluster, click **Nodes** on the left, click the **Node Pools** tab, and click **Create Node Pool** in the upper right corner.

#. Configure the node pool.

   -  **Nodes**: Set it to **1**, indicating that one node is created by default when a node pool is created.
   -  **Specifications**: 2 vCPUs \| 4 GiB

   Retain the defaults for other parameters. For details, see `Creating a Node Pool <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_10_0012.html>`__.

#. Locate the row containing the newly created node pool and click **Auto Scaling** in the upper right corner. For details, see `Creating a Node Scaling Policy <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_10_0209.html>`__.

   If the CCE Cluster Autoscaler add-on is not installed in the cluster, install it first. For details, see `autoscaler <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_10_0154.html>`__.

   -  **Automatic scale-out**: If this function is enabled, nodes in a node pool will be automatically added based on the cluster load.
   -  **Customized Rule**: Click **Add Rule**. In the dialog box displayed, configure parameters. If the CPU allocation rate is greater than 70%, a node is added to each associated node pool. A node scaling policy needs to be associated with a node pool. Multiple node pools can be associated. When you need to scale nodes, node with proper specifications will be added or reduced from the node pool based on the minimum waste principle.
   -  **Automatic scale-in**: If this function is enabled, nodes in a node pool will be automatically deleted based on the cluster load. For example, trigger scale-in when the node resource utilization is less than 50%.
   -  **AS Configuration**: Modify the node quantity range. During autoscaling, the number of nodes in a node pool is always within the configured quantity range.
   -  **AS Object**: Enable autoscaling for node specifications in a node pool.

#. Click **OK**.

Creating a Workload
-------------------

Use the hpa-example image to create a Deployment with one replica. The image path is related to the organization uploaded to the SWR repository and needs to be replaced with the actual value.

.. code-block::

   kind: Deployment
   apiVersion: apps/v1
   metadata:
     name: hpa-example
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: hpa-example
     template:
       metadata:
         labels:
           app: hpa-example
       spec:
         containers:
         - name: container-1
           image: 'hpa-example:latest' # Replace it with the address of the image you uploaded to SWR.
           resources:
             limits:                  # The value of limits must be the same as that of requests to prevent flapping during scaling.
               cpu: 500m
               memory: 200Mi
             requests:
               cpu: 500m
               memory: 200Mi
         imagePullSecrets:
         - name: default-secret

Then, create a NodePort Service for the workload so that the workload can be accessed from external networks.

.. code-block::

   kind: Service
   apiVersion: v1
   metadata:
     name: hpa-example
   spec:
     ports:
       - name: cce-service-0
         protocol: TCP
         port: 80
         targetPort: 80
         nodePort: 31144
     selector:
       app: hpa-example
     type: NodePort

Creating an HPA Policy
----------------------

Create an HPA policy. As shown below, the policy is associated with the hpa-example workload, and the target CPU usage is 50%.

There are two other annotations. One annotation defines the CPU thresholds, indicating that scaling is not performed when the CPU usage is between 30% and 70% to prevent impact caused by slight fluctuation. The other is the scaling time window, indicating that after the policy is successfully executed, a scaling operation will not be triggered again in this cooling interval to prevent impact caused by short-term fluctuation.

.. code-block::

   apiVersion: autoscaling/v2
   kind: HorizontalPodAutoscaler
   metadata:
     name: hpa-policy
     annotations:
       extendedhpa.metrics: '[{"type":"Resource","name":"cpu","targetType":"Utilization","targetRange":{"low":"30","high":"70"}}]'
       extendedhpa.option: '{"downscaleWindow":"5m","upscaleWindow":"3m"}'
   spec:
     scaleTargetRef:
       kind: Deployment
       name: hpa-example
       apiVersion: apps/v1
     minReplicas: 1
     maxReplicas: 100
     metrics:
       - type: Resource
         resource:
           name: cpu
           target:
             type: Utilization
             averageUtilization: 50

Configure the parameters as follows if you are using the console.

|image2|

Observing the Auto Scaling Process
----------------------------------

#. Check the cluster node status. In the following example, there are two nodes.

   .. code-block::

      # kubectl get node
      NAME            STATUS   ROLES    AGE     VERSION
      192.168.0.183   Ready    <none>   2m20s   v1.17.9-r0-CCE21.1.1.3.B001-17.36.8
      192.168.0.26    Ready    <none>   55m     v1.17.9-r0-CCE21.1.1.3.B001-17.36.8

   Check the HPA policy. The CPU usage of the target workload is 0%.

   .. code-block::

      # kubectl get hpa hpa-policy
      NAME         REFERENCE                TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
      hpa-policy   Deployment/hpa-example   0%/50%    1         100       1          4m

#. Run the following command to access the workload. In the following command, {ip:port} indicates the access address of the workload, which can be queried on the workload details page.

   **while true;do wget -q -O- http://**\ *{ip:port}*\ **; done**

   .. note::

      If no EIP is displayed, the cluster node has not been assigned any EIP. Allocate one, bind it to the node, and synchronize node data. .

   Observe the scaling process of the workload.

   .. code-block::

      # kubectl get hpa hpa-policy --watch
      NAME         REFERENCE                TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
      hpa-policy   Deployment/hpa-example   0%/50%     1         100       1          4m
      hpa-policy   Deployment/hpa-example   190%/50%   1         100       1          4m23s
      hpa-policy   Deployment/hpa-example   190%/50%   1         100       4          4m31s
      hpa-policy   Deployment/hpa-example   200%/50%   1         100       4          5m16s
      hpa-policy   Deployment/hpa-example   200%/50%   1         100       4          6m16s
      hpa-policy   Deployment/hpa-example   85%/50%    1         100       4          7m16s
      hpa-policy   Deployment/hpa-example   81%/50%    1         100       4          8m16s
      hpa-policy   Deployment/hpa-example   81%/50%    1         100       7          8m31s
      hpa-policy   Deployment/hpa-example   57%/50%    1         100       7          9m16s
      hpa-policy   Deployment/hpa-example   51%/50%    1         100       7          10m
      hpa-policy   Deployment/hpa-example   58%/50%    1         100       7          11m

   You can see that the CPU usage of the workload is 190% at 4m23s, which exceeds the target value. In this case, scaling is triggered to expand the workload to four replicas/pods. In the subsequent several minutes, the CPU usage does not decrease until 7m16s. This is because the new pods may not be successfully created. The possible cause is that resources are insufficient and the pods are in Pending state. During this period, nodes are added.

   At 7m16s, the CPU usage decreases, indicating that the pods are successfully created and start to bear traffic. The CPU usage decreases to 81% at 8m, still greater than the target value (50%) and the high threshold (70%). Therefore, 7 pods are added at 9m16s, and the CPU usage decreases to 51%, which is within the range of 30% to 70%. From then on, the number of pods remains 7.

   In the following output, you can see the workload scaling process and the time when the HPA policy takes effect.

   .. code-block::

      # kubectl describe deploy hpa-example
      ...
      Events:
        Type    Reason             Age    From                   Message
        ----    ------             ----   ----                   -------
        Normal  ScalingReplicaSet  25m    deployment-controller  Scaled up replica set hpa-example-79dd795485 to 1
        Normal  ScalingReplicaSet  20m    deployment-controller  Scaled up replica set hpa-example-79dd795485 to 4
        Normal  ScalingReplicaSet  16m    deployment-controller  Scaled up replica set hpa-example-79dd795485 to 7
      # kubectl describe hpa hpa-policy
      ...
      Events:
        Type    Reason             Age    From                       Message
        ----    ------             ----   ----                       -------
        Normal  SuccessfulRescale  20m    horizontal-pod-autoscaler  New size: 4; reason: cpu resource utilization (percentage of request) above target
        Normal  SuccessfulRescale  16m    horizontal-pod-autoscaler  New size: 7; reason: cpu resource utilization (percentage of request) above target

   Check the number of nodes. The following output shows that two nodes are added.

   .. code-block::

      # kubectl get node
      NAME            STATUS   ROLES    AGE     VERSION
      192.168.0.120   Ready    <none>   3m5s    v1.17.9-r0-CCE21.1.1.3.B001-17.36.8
      192.168.0.136   Ready    <none>   6m58s   v1.17.9-r0-CCE21.1.1.3.B001-17.36.8
      192.168.0.183   Ready    <none>   18m     v1.17.9-r0-CCE21.1.1.3.B001-17.36.8
      192.168.0.26    Ready    <none>   71m     v1.17.9-r0-CCE21.1.1.3.B001-17.36.8

   You can also view the scaling history on the console. For example, the CA policy is executed once when the CPU allocation rate in the cluster is greater than 70%, and the number of nodes in the node pool is increased from 2 to 3. The new node is automatically added by autoscaler based on the pending state of pods in the initial phase of HPA.

   The node scaling process is as follows:

   a. After the number of pods changes to 4, the pods are in Pending state due to insufficient resources. As a result, the default scale-out policy of the autoscaler add-on is triggered, and the number of nodes is increased by one.
   b. The second node scale-out is triggered because the CPU allocation rate in the cluster is greater than 70%. As a result, the number of nodes is increased by one, which is recorded in the scaling history on the console. Scaling based on the allocation rate ensures that the cluster has sufficient resources.

#. Stop accessing the workload and check the number of pods.

   .. code-block::

      # kubectl get hpa hpa-policy --watch
      NAME         REFERENCE                TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
      hpa-policy   Deployment/hpa-example   50%/50%    1         100       7          12m
      hpa-policy   Deployment/hpa-example   21%/50%    1         100       7          13m
      hpa-policy   Deployment/hpa-example   0%/50%     1         100       7          14m
      hpa-policy   Deployment/hpa-example   0%/50%     1         100       7          18m
      hpa-policy   Deployment/hpa-example   0%/50%     1         100       3          18m
      hpa-policy   Deployment/hpa-example   0%/50%     1         100       3          19m
      hpa-policy   Deployment/hpa-example   0%/50%     1         100       3          19m
      hpa-policy   Deployment/hpa-example   0%/50%     1         100       3          19m
      hpa-policy   Deployment/hpa-example   0%/50%     1         100       3          19m
      hpa-policy   Deployment/hpa-example   0%/50%     1         100       3          23m
      hpa-policy   Deployment/hpa-example   0%/50%     1         100       3          23m
      hpa-policy   Deployment/hpa-example   0%/50%     1         100       1          23m

   You can see that the CPU usage is 21% at 13m. The number of pods is reduced to 3 at 18m, and then reduced to 1 at 23m.

   In the following output, you can see the workload scaling process and the time when the HPA policy takes effect.

   .. code-block::

      # kubectl describe deploy hpa-example
      ...
      Events:
        Type    Reason             Age    From                   Message
        ----    ------             ----   ----                   -------
        Normal  ScalingReplicaSet  25m    deployment-controller  Scaled up replica set hpa-example-79dd795485 to 1
        Normal  ScalingReplicaSet  20m    deployment-controller  Scaled up replica set hpa-example-79dd795485 to 4
        Normal  ScalingReplicaSet  16m    deployment-controller  Scaled up replica set hpa-example-79dd795485 to 7
        Normal  ScalingReplicaSet  6m28s  deployment-controller  Scaled down replica set hpa-example-79dd795485 to 3
        Normal  ScalingReplicaSet  72s    deployment-controller  Scaled down replica set hpa-example-79dd795485 to 1
      # kubectl describe hpa hpa-policy
      ...
      Events:
        Type    Reason             Age    From                       Message
        ----    ------             ----   ----                       -------
        Normal  SuccessfulRescale  20m    horizontal-pod-autoscaler  New size: 4; reason: cpu resource utilization (percentage of request) above target
        Normal  SuccessfulRescale  16m    horizontal-pod-autoscaler  New size: 7; reason: cpu resource utilization (percentage of request) above target
        Normal  SuccessfulRescale  6m45s  horizontal-pod-autoscaler  New size: 3; reason: All metrics below target
        Normal  SuccessfulRescale  90s    horizontal-pod-autoscaler  New size: 1; reason: All metrics below target

   You can also view the HPA policy execution history on the console. Wait until the one node is reduced.

   The reason why the other two nodes in the node pool are not reduced is that they both have pods in the kube-system namespace (and these pods are not created by DaemonSets). For details, see `Node Scaling Mechanisms <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_10_0296.html>`__.

Summary
-------

Using HPA and CA can easily implement auto scaling in most scenarios. In addition, the scaling process of nodes and pods can be easily observed.

.. |image1| image:: /_static/images/en-us_image_0000001898024157.png
.. |image2| image:: /_static/images/en-us_image_0000001851585272.png
