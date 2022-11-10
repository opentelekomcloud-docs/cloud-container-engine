:original_name: cce_01_0395.html

.. _cce_01_0395:

Switching from AOM to HPA for Auto Scaling
==========================================

CCE clusters of v1.15 or earlier support workload scaling based on AOM monitoring data. This function is no longer supported in CCE clusters of v1.17 or later.

If you have configured auto scaling based on AOM, you can switch to :ref:`HPA <cce_01_0208>` policies after your cluster is upgraded to v1.17. Note the following differences during the switchover:

-  In AOM-based auto scaling, resource usage rate is calculated based on the limit of a workload, from 0% to 100%.

For example, if the memory request of a workload is 2 GB and the memory limit is 16 GB, a scale-out is triggered as long as the memory utilization reaches 50% of the limit (8 GB) in AOM-based auto scaling. In HPA-based scaling, you need to set the memory usage rate to 400% (16 x 50%/2) to trigger the same scaling.
