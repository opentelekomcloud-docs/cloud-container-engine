:original_name: cce_bestpractice_00282.html

.. _cce_bestpractice_00282:

Using an HPA and a CA for Auto Scaling of Workloads and Nodes
=============================================================

Application Scenarios
---------------------

The best way to handle surging traffic is to automatically adjust the number of machines based on the traffic volume or resource usage, which is called scaling.

To prevent pods from using up node resources during peak hours, it is a common practice to specify resource requests and limits for pods during application containerization. However, this approach may encounter a resource bottleneck as an application exception may occur once the upper limit of resource usage is reached. To resolve this problem, you can scale the number pods for each workload. If the node resource usage reaches its upper limit after pods are added and new pods cannot be scheduled, you can scale the nodes based on the node resource usage.

Solution
--------

There are two major auto scaling policies in CCE: Horizontal Pod Autoscaler (HPA) and Cluster Autoscaler (CA). An HPA scales workloads, and a CA scales nodes.

HPAs and CAs work together to optimize the resource allocation in a cluster. A CA adds nodes to provide enough resources for an HPA to scale out pods. When the HPA reduces the number of pods in the cluster, the CA removes underutilized nodes.

As shown in :ref:`Figure 1 <cce_bestpractice_00282__fig6540132372015>`, the HPA automatically increases the number of pods based on metrics. If there are not enough resources in the cluster, new pods will be pending. The CA will then review these pending pods and choose the most appropriate node pool to scale out nodes based on the configured scaling policy.

.. _cce_bestpractice_00282__fig6540132372015:

.. figure:: /_static/images/en-us_image_0000002631531532.png
   :alt: **Figure 1** HPA and CA working flows

   **Figure 1** HPA and CA working flows

Using HPAs together with CAs makes it easy to achieve auto scaling, and the scaling process of both nodes and pods can be observed very intuitively. This approach is sufficient for most service scenarios.

This section uses an example to describe the auto scaling process using HPAs and CAs together.

Preparations
------------

#. Create a single-node cluster with at least 2 vCPUs and 4 GiB of memory. Assign an EIP for external access. If no EIP is associated during node creation, bind one manually via the ECS console afterward.
#. Install add-ons in the cluster.

   -  CCE Cluster Autoscaler: scales nodes in the cluster.
   -  Kubernetes Metrics Server: aggregates resource usage data in the cluster. It can collect measurement data for important Kubernetes resources like pods, nodes, containers, and Services.

#. Log in to the node and deploy a computing-intensive application. When a user sends a request, the result needs to be calculated before being returned to the user.

   a. Create a PHP file named **index.php** to calculate the square root of the request 1,000,000 times before returning **OK!**.

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

   b. Write a Dockerfile to build an image.

      .. code-block::

         vi Dockerfile

      The content is as follows:

      .. code-block::

         FROM php:5-apache
         COPY index.php /var/www/html/index.php
         RUN chmod a+rx index.php

   c. Build an image named **hpa-example** with the **latest** tag.

      .. code-block::

         docker build -t hpa-example:latest .

   d. .. _cce_bestpractice_00282__li108181514125:

      (Optional) Log in to the SWR console, choose **Organizations** in the navigation pane, and click **Create Organization** in the upper right corner.

      Skip this step if you already have an organization.

   e. .. _cce_bestpractice_00282__li187221141362:

      In the navigation pane, choose **My Images** and then click **Upload Through Client**. In the dialog box displayed, click **Generate Login Command** and click |image1| to copy the command.

   f. Run the copied login command on the node. If the login is successful, the message "Login Succeeded" is displayed.

   g. Tag the hpa-example image.

      **docker tag** *[image name 1*:*tag 1]*/*[image-repository-address]*/*[organization name]*/*[image name 2*:*tag 2]*

      -  *[image name 1:tag 1]*: name and tag of the local image to be pushed.
      -  *[image-repository-address]*: The domain name at the end of the :ref:`login command <cce_bestpractice_00282__li187221141362>`. The image repository address can be obtained on the SWR console.
      -  *[organization name]*: name of the :ref:`created organization <cce_bestpractice_00282__li108181514125>`.
      -  *[image name 2:tag 2]*: target image name and tag to be displayed on the SWR console.

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

#. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Nodes**. In the right pane, click the **Node Pools** tab and click **Create Node Pool** in the upper right corner.

#. Configure the node pool.

   -  **Node Type**: Select a node type.
   -  **Specifications**: 2 vCPUs \| 4 GiB

   Retain the default values for other parameters. For details, see `Creating a Node Pool <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_10_0012.html>`__.

#. Locate the row containing the newly created node pool and click **Auto Scaling** in the upper right corner. For details, see `Creating a Node Auto Scaling Policy <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_10_0209.html>`__.

   If the CCE Cluster Autoscaler add-on is not installed in the cluster, install it first. For details, see `CCE Cluster Autoscaler <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_10_0154.html>`__.

   -  **Customize scale-out rules.**: Click **Add Rule**. In the dialog box displayed, configure parameters. For example, you can configure a rule that when the CPU allocation rate exceeds 70%, each associated node pool will scale out by adding one node. A CA policy must be associated with one or more node pools. When node scaling is required, CA selects the most suitable node flavors from the associated node pools based on the least waste principle.
   -  **Nodes**: Change the node quantity range. The number of nodes in a node pool will always be within the range during auto scaling.
   -  **Cooldown Period**: a period during which the nodes added to the node pool cannot be scaled in
   -  **Specifications**: Configure whether to enable auto scaling for a node flavor in the node pool.

#. Click **OK**.

Creating a Workload
-------------------

Use the hpa-example image to create a Deployment with one pod. The image address is related to the organization uploaded to the SWR repository and needs to be replaced with the actual value.

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
           image: ''hpa-example:latest'' # Replace it with the address of the image you uploaded to SWR.
           resources:
             limits:                  # The value of limits must be the same as that of requests to prevent flapping during scaling.
               cpu: 500m
               memory: 200Mi
             requests:
               cpu: 500m
               memory: 200Mi
         imagePullSecrets:
         - name: default-secret

Create a NodePort Service for the workload so that the workload can be accessed from external networks.

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

Create an HPA policy. The policy below is associated with the **hpa-example** workload, and the target CPU usage is 50%.

There are also two annotations. One defines the CPU thresholds. It indicates that scaling is not performed when the CPU usage is between 30% and 70% to prevent impact caused by minor fluctuations. The other defines the scaling time window. It indicates that after a scaling event is successfully triggered, no additional scale-in or scale-out actions occur during the cooldown period to prevent impact caused by short-term fluctuations.

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

Configure the parameters as shown in the figure below if you are using the console.

|image2|

Observing the Auto Scaling Process
----------------------------------

#. Check the cluster node statuses. In the following example, there are two nodes.

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

#. Access the workload. In the command below, *{ip:port}* indicates the access address of the workload, which can be obtained on the workload details page.

   **while true;do wget -q -O- http://**\ *{ip:port}*\ **; done**

   .. note::

      If no EIP is displayed, the cluster node has no EIP associated. Assign an EIP, bind it to the node, and synchronize node data.

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

   At 4m23s, the CPU usage of the workload reached 190%, exceeding the target value. This triggered workload scaling, increasing the workload to four replicas/pods. Over the next few minutes, CPU usage did not drop. It was not until 7m16s that the CPU usage began to decrease. This is because newly created pods may not start successfully. If resources are insufficient, the pods remain in a pending state while the system is scaling out the nodes.

   At 7m16s, the drop in CPU usage indicates that the pods were successfully created and began sharing the incoming traffic. By the 8-minute mark, the CPU usage had fallen to 81%, which is still above the target value and above 70%. This means another scale-out event would occur. At 9m16s, the workload scaled out again to seven pods. At that point, the CPU usage dropped to 51%, which falls within the 30%-70% range, so no further scaling actions were taken. From that point on, the number of pods remained stable at seven.

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

   Check the number of nodes. The following output shows that two nodes have been added.

   .. code-block::

      # kubectl get node
      NAME            STATUS   ROLES    AGE     VERSION
      192.168.0.120   Ready    <none>   3m5s    v1.17.9-r0-CCE21.1.1.3.B001-17.36.8
      192.168.0.136   Ready    <none>   6m58s   v1.17.9-r0-CCE21.1.1.3.B001-17.36.8
      192.168.0.183   Ready    <none>   18m     v1.17.9-r0-CCE21.1.1.3.B001-17.36.8
      192.168.0.26    Ready    <none>   71m     v1.17.9-r0-CCE21.1.1.3.B001-17.36.8

   You can also view the scaling history on the console. Here, you can see that the CA policy was triggered once. When the cluster's CPU allocation exceeded 70%, the number of nodes in the node pool was scaled out from two to three. The other new node was added by the autoscaler's default behavior, which scales based on pods in the pending state during the early phase of HPA scaling.

   The node scaling process is as follows:

   a. After the number of pods increased to four, there were not enough resources available, causing the pods to remain in a pending state. This triggered the autoscaler's default scaling behavior, which added one more node.
   b. The second node scaling event occurred because the cluster's CPU allocation exceeded 70%, which triggered the CA policy and added another node. This can be seen in the scaling history on the console. Scaling based on CPU allocation ensures that the cluster consistently maintains sufficient resources.

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

   You can see that the CPU usage is 21% at 13m. The number of pods is reduced to three at 18m, and then reduced to one at 23m.

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

   You can also view the HPA scaling history on the console. If you continue to wait, you will see that one of the nodes is scaled in.

   Scale-in was blocked because both nodes in the node pool are running non-DaemonSet pods in the **kube-system** namespace. For details about the conditions that block scale-in operations, see `Node Scaling Rules <https://docs.otc.t-systems.com/en-us/usermanual2/cce/cce_10_0296.html>`__.

Summary
-------

By using HPAs and CAs, auto scaling can be effortlessly implemented in various scenarios. Additionally, the scaling process of nodes and pods can be conveniently tracked.

.. |image1| image:: /_static/images/en-us_image_0000002661850715.png
.. |image2| image:: /_static/images/en-us_image_0000002662010665.png
