:original_name: cce_10_0279.html

.. _cce_10_0279:

Overview
========

Auto scaling is a service that automatically and economically adjusts service resources based on your service requirements and configured policies.

Context
-------

More and more applications are developed based on Kubernetes. It becomes increasingly important to quickly scale out applications on Kubernetes to cope with service peaks and to scale in applications during off-peak hours to save resources and reduce costs.

In a Kubernetes cluster, auto scaling involves pods and nodes. A pod is an application instance. Each pod contains one or more containers and runs on a node (VM or bare-metal server). If a cluster does not have sufficient nodes to run new pods, you need to add nodes to the cluster to ensure service running.

In CCE, auto scaling is used for online services, large-scale computing and training, deep learning GPU or shared GPU training and inference, periodic load changes, and many other scenarios.

Auto Scaling in CCE
-------------------

**CCE supports auto scaling for workloads and nodes.**

-  :ref:`Workload scaling <cce_10_0290>`\ **:** Auto scaling at the scheduling layer to change the scheduling capacity of workloads. For example, you can use the HPA, a scaling component at the scheduling layer, to adjust the number of replicas of an application. Adjusting the number of replicas changes the scheduling capacity occupied by the current workload, thereby enabling scaling at the scheduling layer.
-  :ref:`Node scaling <cce_10_0296>`\ **:** Auto scaling at the resource layer. When the planned cluster nodes cannot allow workload scheduling, ECS resources are provided to support scheduling.

Components
----------

**Workload scaling components are described as follows:**

.. table:: **Table 1** Workload scaling components

   +------+-------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------+
   | Type | Component Name                      | Component Description                                                                                                                                                              | Reference                                                             |
   +======+=====================================+====================================================================================================================================================================================+=======================================================================+
   | HPA  | :ref:`metrics-server <cce_10_0205>` | A built-in component of Kubernetes, which enables horizontal scaling of pods. It adds the application-level cooldown time window and scaling threshold functions based on the HPA. | :ref:`Creating an HPA Policy for Workload Auto Scaling <cce_10_0208>` |
   +------+-------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------+

**Node scaling components are described as follows:**

.. table:: **Table 2** Node scaling components

   +---------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+-----------------------------------------------------+
   | Component Name                  | Component Description                                                                                                                         | Application Scenario                                                                    | Reference                                           |
   +=================================+===============================================================================================================================================+=========================================================================================+=====================================================+
   | :ref:`autoscaler <cce_10_0154>` | An open source Kubernetes component for horizontal scaling of nodes, which is optimized in terms of scheduling and auto scaling capabilities. | Online services, deep learning, and large-scale computing with limited resource budgets | :ref:`Creating a Node Scaling Policy <cce_10_0209>` |
   +---------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+-----------------------------------------------------------------------------------------+-----------------------------------------------------+
