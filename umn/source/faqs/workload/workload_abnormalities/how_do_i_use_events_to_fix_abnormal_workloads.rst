:original_name: cce_faq_00134.html

.. _cce_faq_00134:

How Do I Use Events to Fix Abnormal Workloads?
==============================================

If a workload is abnormal, you can check the pod events first to locate the fault and then rectify the fault.

Fault Locating
--------------

|image1|

To check whether there is an abnormal pod in the workload, perform the following steps:

#. Log in to the CCE console.
#. Click the cluster name to access the cluster console. In the navigation pane, choose **Workloads**.
#. In the upper left corner of the page, select a namespace, locate the target workload, and view its status.

   -  If the workload is not ready, view pod events, and determine the cause. For details, see :ref:`Viewing Pod Events <cce_faq_00134__section13566155892120>`.

   -  If the workload is processing, wait patiently.

   -  If the workload is running, no action is required. If the workload status is normal but it cannot be accessed, check whether intra-cluster access is normal.

      Log in to the CCE console or use kubectl to obtain the pod IP address. Then, log in to the node where this pod locates and run **curl** or use other methods to manually call the APIs. Check whether the expected result is returned.

      If *{Container IP address}*\ **:** *{Port}* cannot be accessed, log in to the service container and access **127.0.0.1**: *{Port}* to locate the fault.

.. _cce_faq_00134__section13566155892120:

Viewing Pod Events
------------------

**Method 1**

On the CCE console, click the workload name to go to the workload details page, locate the row containing the abnormal pod, and click **View Events** in the **Operation** column.

**Method 2**

Run **kubectl describe pod** *{Pod name}* to view pod events. The following shows an example:

.. code-block::

   $ kubectl describe pod prepare-58bd7bdf9-fthrp
   ...
   Events:
     Type     Reason            Age   From               Message
     ----     ------            ----  ----               -------
     Warning  FailedScheduling  49s   default-scheduler  0/2 nodes are available: 2 Insufficient cpu.
     Warning  FailedScheduling  49s   default-scheduler  0/2 nodes are available: 2 Insufficient cpu.

.. table:: **Table 1** Troubleshooting methods

   +-----------------------------------------------------------------+-----------------------+----------------------------------------------------------------------------------------------------------------------------+
   | Event                                                           | Pod Status            | Solution                                                                                                                   |
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

.. |image1| image:: /_static/images/en-us_image_0000001898024145.png
