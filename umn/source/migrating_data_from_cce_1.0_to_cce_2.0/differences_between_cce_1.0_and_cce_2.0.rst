:original_name: cce_01_9998.html

.. _cce_01_9998:

Differences Between CCE 1.0 and CCE 2.0
=======================================

CCE 2.0 inherits and modifies the features of CCE 1.0, and release new features.

-  Modified features:

   -  Clusters in CCE 1.0 are equivalent to Hybrid clusters in CCE 2.0.
   -  CCE 2.0 does not have the concept of component template. In CCE 1.0, you can set parameters in the component template when creating an application.
   -  Applications in CCE 1.0 are equivalent to Deployments in CCE 2.0. In addition, the concept of StatefulSet is added in CCE 2.0.

-  New Features:

   -  EVS disks and file storage can be created and directly mounted during workload creation on the CCE console.
   -  Auto scaling for cluster nodes.
   -  Docker upgraded to the 1706 version.
   -  Customized helm charts for orchestrating workloads.

-  In CCE 2.0, before uploading an image, you must create an image repository.

The following table compares the two versions.

.. table:: **Table 1** Comparison between CCE 1.0 and CCE 2.0

   ================== ====================================
   CCE 1.0            CCE 2.0
   ================== ====================================
   Container registry Image repository
   Cluster            Resource Management > Hybrid Cluster
   Component template -
   App Designer       -
   App Manager        Deployment
   ================== ====================================
