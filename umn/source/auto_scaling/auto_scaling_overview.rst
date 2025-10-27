:original_name: cce_10_0279.html

.. _cce_10_0279:

Auto Scaling Overview
=====================

As applications increasingly run on Kubernetes, the ability to rapidly scale out during peak times and scale in during off-peak hours becomes crucial for efficiently managing resources and reducing costs.

Auto scaling is widely used in CCE. Typical use cases are as follows:

-  **Online service scaling**: Pods and nodes are automatically scaled out during peak hours (for example, holidays and promotions) to handle increased user requests, and scaled in during off-peak hours to cut costs.
-  **Large-scale computing training**: The number of pods and nodes is dynamically adjusted according to the demands of computing tasks to accelerate execution.
-  **Deep learning GPU training and inference**: GPU resources are dynamically allocated to optimize the efficiency of training and inference tasks. GPU nodes are automatically added or removed as needed to enhance resource utilization.
-  **Scheduled or periodic resource adjustment**: Pods and nodes are automatically scaled at specific times to accommodate scheduled tasks. Resources are dynamically adjusted based on task requirements to ensure smooth execution.

Auto Scaling in CCE
-------------------

**CCE supports auto scaling for workloads and nodes.**

-  :ref:`Workload scaling <cce_10_0290>` involves adjusting the number or specifications of pods at the scheduling layer to adapt to changes in workload demands. For example, the number of pods can be automatically increased during peak hours to handle more user requests and then scaled down during off-peak hours to reduce costs.
-  :ref:`Node scaling <cce_10_0296>` involves dynamically adding or reducing compute resources (such as ECSs) at the resource layer based on the scheduling status of pods. This approach ensures that clusters are well-resourced for high loads and minimizes waste during low demand.

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
   | AHPA    | :ref:`CCE Advanced HPA <cce_10_0240>`                   | Advanced Horizontal Pod Autoscaler, which performs scaling beforehand based on historical data.                                                                                                                      | :ref:`Creating an AHPA Policy <cce_10_0934>`             |
   +---------+---------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+----------------------------------------------------------+

**Node Scaling Types**

.. table:: **Table 2** Node scaling types

   +---------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+-----------------------------------------------------+
   | Component Name                              | Description                                                                                                                            | Application Scenario                                                                    | Reference                                           |
   +=============================================+========================================================================================================================================+=========================================================================================+=====================================================+
   | :ref:`CCE Cluster Autoscaler <cce_10_0154>` | An open-source Kubernetes component for horizontal scaling of nodes, which is optimized by CCE in scheduling, auto scaling, and costs. | Online services, deep learning, and large-scale computing with limited resource budgets | :ref:`Creating a Node Scaling Policy <cce_10_0209>` |
   +---------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+-----------------------------------------------------+
