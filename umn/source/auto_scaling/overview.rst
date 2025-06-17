:original_name: cce_10_0279.html

.. _cce_10_0279:

Overview
========

Auto scaling is a service that automatically and economically adjusts service resources based on your service requirements and configured policies.

Context
-------

More and more applications run on Kubernetes. It becomes increasingly important to quickly scale out applications on Kubernetes to cope with service peaks and to scale in applications during off-peak hours to save resources and reduce costs.

In a Kubernetes cluster, auto scaling involves pods and nodes. A pod is an application instance. Each pod contains one or more containers and runs on a node (VM or bare-metal server). If a cluster does not have sufficient nodes to run new pods, add nodes to the cluster to ensure service running.

In CCE, auto scaling is used for online services, large-scale computing and training, deep learning GPU or shared GPU training and inference, periodic load changes, and many other scenarios.

Auto Scaling in CCE
-------------------

**CCE supports auto scaling for workloads and nodes.**

-  :ref:`Workload scaling <cce_10_0290>`\ **:** Auto scaling at the scheduling layer to change the scheduling capacity of workloads. For example, you can use the HPA, a scaling component at the scheduling layer, to adjust the number of replicas of an application. Adjusting the number of replicas changes the scheduling capacity occupied by the current workload, thereby enabling scaling at the scheduling layer.
-  :ref:`Node scaling <cce_10_0296>`\ **:** Auto scaling at the resource layer. When the planned cluster nodes cannot allow workload scheduling, ECS resources are provided to support scheduling.

Components
----------

**Workload Scaling Types**

.. table:: **Table 1** Workload scaling types

   +---------+---------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------+
   | Type    | Component                                               | Description                                                                                                                                                                                                          | Reference                                                |
   +=========+=========================================================+======================================================================================================================================================================================================================+==========================================================+
   | HPA     | HorizontalPodAutoscaler (built-in Kubernetes component) | HorizontalPodAutoscaler is a built-in component of Kubernetes for Horizontal Pod Autoscaling (HPA). CCE incorporates the application-level cooldown time window and scaling threshold functions into Kubernetes HPA. | :ref:`Creating an HPA Policy <cce_10_0208>`              |
   +---------+---------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------+
   | CronHPA | :ref:`CCE Advanced HPA <cce_10_0240>`                   | CronHPA can scale in or out a cluster at a fixed time. It can work with HPA policies to periodically adjust the HPA scaling scope, implementing workload scaling in complex scenarios.                               | :ref:`Creating a Scheduled CronHPA Policy <cce_10_0415>` |
   +---------+---------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------+

**Node Scaling Types**

.. table:: **Table 2** Node scaling types

   +---------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+-----------------------------------------------------+
   | Component Name                              | Description                                                                                                                            | Application Scenario                                                                    | Reference                                           |
   +=============================================+========================================================================================================================================+=========================================================================================+=====================================================+
   | :ref:`CCE Cluster Autoscaler <cce_10_0154>` | An open source Kubernetes component for horizontal scaling of nodes, which is optimized by CCE in scheduling, auto scaling, and costs. | Online services, deep learning, and large-scale computing with limited resource budgets | :ref:`Creating a Node Scaling Policy <cce_10_0209>` |
   +---------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+-----------------------------------------------------+
