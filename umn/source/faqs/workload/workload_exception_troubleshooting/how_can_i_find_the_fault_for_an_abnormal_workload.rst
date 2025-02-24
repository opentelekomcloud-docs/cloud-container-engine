:original_name: cce_faq_00134.html

.. _cce_faq_00134:

How Can I Find the Fault for an Abnormal Workload?
==================================================

If a workload is abnormal, you can check the pod events first to locate the fault and then rectify the fault.

Fault Locating
--------------

|image1|

To locate the fault of an abnormal workload, take the following steps:

#. **Check whether the pod is running properly.**

   a. Log in to the CCE console.
   b. Click the cluster name to access the cluster console. In the navigation pane, choose **Workloads**.
   c. In the upper left corner of the page, select a namespace, locate the target workload, and view its status.

      -  If the workload is not ready, you can view pod events and determine the cause. For details, see :ref:`Viewing Pod Events <cce_faq_00134__section13566155892120>`. You can find the solution to the exception based on the events by referring to :ref:`Common Pod Issues <cce_faq_00134__section16218123114816>`.
      -  If the workload is processing, wait patiently.
      -  If the workload is running, no action is required. If the workload status is normal but it cannot be accessed, check whether intra-cluster access is normal.

#. **Check whether access within the cluster is normal.**

   Log in to the CCE console or use kubectl to obtain the pod IP address. Then, log in to the node or the pod and run **curl** or use other methods to manually call the APIs and check whether the expected result is returned.

   If *{Container IP address}*\ **:**\ *{Port number}* cannot be accessed, log in to the service container, access **127.0.0.1**:*{Port number}*, and locate the fault.

#. **Check whether the access result meets the expectation.**

   If the workload is accessible within the cluster but the access result is not as expected, check the workload configurations, such as verifying if the image tag and environment variables are correctly configured.

.. _cce_faq_00134__section16218123114816:

Common Pod Issues
-----------------

+-----------------------+---------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
| Status                | Description                                                         | Solution                                                                                                                   |
+=======================+=====================================================================+============================================================================================================================+
| Pending               | The pod scheduling failed.                                          | For details, see :ref:`What Should I Do If Pod Scheduling Fails? <cce_faq_00098>`                                          |
+-----------------------+---------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
| Pending               | A storage volume fails to be mounted to a pod.                      | For details, see :ref:`What Should I Do If a Storage Volume Cannot Be Mounted or the Mounting Times Out? <cce_faq_00200>`. |
+-----------------------+---------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
| FailedPullImage       | The image pull failed.                                              | For details, see :ref:`What Should I Do If a Pod Fails to Pull the Image? <cce_faq_00015>`                                 |
|                       |                                                                     |                                                                                                                            |
| ImagePullBackOff      | The image failed to be pulled again.                                |                                                                                                                            |
+-----------------------+---------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
| CreateContainerError  | The container startup failed.                                       | For details, see :ref:`What Should I Do If Container Startup Fails? <cce_faq_00018>`                                       |
|                       |                                                                     |                                                                                                                            |
| CrashLoopBackOff      | The container failed to restart.                                    |                                                                                                                            |
+-----------------------+---------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
| Evicted               | A pod is in the **Evicted** state, and the pod keeps being evicted. | For details, see :ref:`What Should I Do If a Pod Fails to Be Evicted? <cce_faq_00209>`                                     |
+-----------------------+---------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
| Creating              | A pod is in the **Creating** state.                                 | For details, see :ref:`What Should I Do If a Workload Remains in the Creating State? <cce_faq_00140>`                      |
+-----------------------+---------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
| Terminating           | A pod is in the **Terminating** state.                              | For details, see :ref:`What Should I Do If a Pod Remains in the Terminating State? <cce_faq_00210>`                        |
+-----------------------+---------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
| Stopped               | A pod is in the **Stopped** state.                                  | For details, see :ref:`What Should I Do If a Workload Is Stopped Caused by Pod Deletion? <cce_faq_00012>`                  |
+-----------------------+---------------------------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+

.. _cce_faq_00134__section13566155892120:

Viewing Pod Events
------------------

**Method 1**

On the CCE console, click the workload name to go to the workload details page, locate the row containing the abnormal pod, and choose **More** > **View Events** in the **Operation** column.

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

.. |image1| image:: /_static/images/en-us_image_0000002101677565.png
