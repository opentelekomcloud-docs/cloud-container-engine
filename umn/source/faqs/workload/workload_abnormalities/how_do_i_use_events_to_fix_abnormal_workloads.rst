:original_name: cce_faq_00134.html

.. _cce_faq_00134:

How Do I Use Events to Fix Abnormal Workloads?
==============================================

If a workload is abnormal, you can first check the pod events to locate the fault and then rectify the fault by referring to :ref:`Table 1 <cce_faq_00134__table169664177533>`.

.. _cce_faq_00134__table169664177533:

.. table:: **Table 1** Troubleshooting methods

   +-----------------------------------------------------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------+
   | Event Information                                               | Pod Status            | Solution                                                                                                                   |
   +=================================================================+=======================+============================================================================================================================+
   | PodsScheduling failed                                           | Pending               | For details, see :ref:`What Should I Do If Pod Scheduling Fails? <cce_faq_00098>`.                                         |
   +-----------------------------------------------------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------+
   | PodsFailed to pull image                                        | FailedPullImage       | For details, see :ref:`What Should I Do If a Pod Fails to Pull the Image? <cce_faq_00015>`.                                |
   |                                                                 |                       |                                                                                                                            |
   | Failed to re-pull image                                         | ImagePullBackOff      |                                                                                                                            |
   +-----------------------------------------------------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------+
   | PodsCreation failed                                             | CreateContainerError  | For details, see :ref:`What Should I Do If Container Startup Fails? <cce_faq_00018>`.                                      |
   |                                                                 |                       |                                                                                                                            |
   | Failed to restart container                                     | CrashLoopBackOff      |                                                                                                                            |
   +-----------------------------------------------------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------+
   | The pod status is **Evicted**, and the pod keeps being evicted. | Evicted               | For details, see :ref:`What Should I Do If a Pod Fails to Be Evicted? <cce_faq_00209>`.                                    |
   +-----------------------------------------------------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------+
   | The storage volume fails to be mounted to the pod.              | Pending               | For details, see :ref:`What Should I Do If a Storage Volume Cannot Be Mounted or the Mounting Times Out? <cce_faq_00200>`. |
   +-----------------------------------------------------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------+
   | The pod stays **Creating**.                                     | Creating              | For details, see :ref:`What Should I Do If a Workload Remains in the Creating State? <cce_faq_00140>`.                     |
   +-----------------------------------------------------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------+
   | The pod stays **Terminating**.                                  | Terminating           | For details, see :ref:`What Should I Do If Pods in the Terminating State Cannot Be Deleted? <cce_faq_00210>`.              |
   +-----------------------------------------------------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------+
   | The pod status is **Stopped**.                                  | Stopped               | For details, see :ref:`What Should I Do If a Workload Is Stopped Caused by Pod Deletion? <cce_faq_00012>`.                 |
   +-----------------------------------------------------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------+

.. _cce_faq_00134__section13566155892120:

Viewing Pod Events
------------------

Run the **kubectl describe pod** *{pod-name}* command to view pod events, or log in to the CCE console and view pod events on the workload details page.

.. code-block::

   $ kubectl describe pod prepare-58bd7bdf9-fthrp
   ...
   Events:
     Type     Reason            Age   From               Message
     ----     ------            ----  ----               -------
     Warning  FailedScheduling  49s   default-scheduler  0/2 nodes are available: 2 Insufficient cpu.
     Warning  FailedScheduling  49s   default-scheduler  0/2 nodes are available: 2 Insufficient cpu.
