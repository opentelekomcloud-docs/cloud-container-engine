:original_name: cce_faq_00018.html

.. _cce_faq_00018:

Failed to Restart a Container
=============================

On the details page of a workload, if an event is displayed indicating that the container fails to be restarted, perform the following operations to locate the fault:

#. Log in to the node where the abnormal workload is located.

#. Check the ID of the container where the workload pod exits abnormally.

   .. code-block::

      docker ps -a | grep $podName

#. View the logs of the corresponding container.

   .. code-block::

      docker logs $containerID

   Rectify the fault of the workload based on logs.

#. Check the error logs.

   .. code-block::

      cat /var/log/messages | grep $containerID  | grep oom

   Check whether the system OOM is triggered based on the logs.

Fault Locating
--------------

-  :ref:`Check Item 1: Whether There Are Processes that Keep Running in the Container <cce_faq_00018__section2524165018111>`
-  :ref:`Check Item 2: Whether Health Check Fails to Be Performed <cce_faq_00018__section1766510426482>`
-  :ref:`Check Item 3: Whether the User Service Has a Bug <cce_faq_00018__section1833513213713>`
-  :ref:`Check Item 4: Whether the Upper Limit of Container Resources Has Been Reached <cce_faq_00018__section060854916109>`
-  :ref:`Check Item 5: Whether the Container Disk Space Is Insufficient <cce_faq_00018__section169421237111219>`
-  :ref:`Check Item 6: Whether the Resource Limits Are Improperly Set for the Container <cce_faq_00018__section1548114151414>`
-  :ref:`Check Item 7: Whether the Container Ports in the Same Pod Conflict with Each Other <cce_faq_00018__section17679197145618>`
-  :ref:`Check Item 8: Whether the Container Startup Command Is Correctly Configured <cce_faq_00018__section1842111295128>`

.. _cce_faq_00018__section2524165018111:

Check Item 1: Whether There Are Processes that Keep Running in the Container
----------------------------------------------------------------------------

#. Log in to the node where the abnormal workload is located.

#. View the container status.

   .. code-block::

      docker ps -a | grep $podName

   Example:

   |image1|

   If no running process in the container, the status code **Exited (0)** is displayed.

.. _cce_faq_00018__section1766510426482:

Check Item 2: Whether Health Check Fails to Be Performed
--------------------------------------------------------

The health check configured for a workload is performed on services periodically. If an exception occurs, the pod reports an event and the pod fails to be restarted.

If the liveness-type (workload liveness probe) health check is configured for the workload and the number of health check failures exceeds the threshold, the containers in the pod will be restarted. On the workload details page, if K8s events contain **Liveness probe failed: Get http...**, the health check fails.

**Solution**

On the workload details page, choose **Upgrade** > **Advanced Settings** > **Health Check** to check whether the health check policy is properly set and whether services are normal.

.. _cce_faq_00018__section1833513213713:

Check Item 3: Whether the User Service Has a Bug
------------------------------------------------

Check whether the workload startup command is correctly executed or whether the workload has a bug.

#. Log in to the node where the abnormal workload is located.

#. Check the ID of the container where the workload pod exits abnormally.

   .. code-block::

      docker ps -a | grep $podName

#. View the logs of the corresponding container.

   .. code-block::

      docker logs $containerID

   Note: In the preceding command, **containerID** indicates the ID of the container that has exited.


   .. figure:: /_static/images/en-us_image_0000001223473849.png
      :alt: **Figure 1** Incorrect startup command of the container

      **Figure 1** Incorrect startup command of the container

   As shown above, the container fails to be started due to an incorrect startup command. For other errors, rectify the bugs based on the logs.

   Solution: Re-create a workload and configure a correct startup command.

.. _cce_faq_00018__section060854916109:

Check Item 4: Whether the Upper Limit of Container Resources Has Been Reached
-----------------------------------------------------------------------------

If the upper limit of container resources has been reached, OOM will be displayed in the event details as well as in the log:

.. code-block::

   cat /var/log/messages | grep 96feb0a425d6 | grep oom

|image2|

When a workload is created, if the requested resources exceed the configured upper limit, the system OOM is triggered and the container exits unexpectedly.

.. _cce_faq_00018__section169421237111219:

Check Item 5: Whether the Container Disk Space Is Insufficient
--------------------------------------------------------------

The following message refers to the Thin Pool disk that is allocated from the Docker disk selected during node creation. You can run the **lvs** command as user **root** to view the current disk usage.

.. code-block::

   Thin Pool has 15991 free data blocks which is less than minimum required 16383 free data blocks. Create more free space in thin pool or use dm.min_free_space option to change behavior

|image3|

**Solution**

#. Release used disk space.

   .. code-block::

      docker rmi -f `docker images | grep 20202 | awk '{print $3}'`

#. Expand the disk capacity. For details, see the method of expanding the data disk capacity of a node.

.. _cce_faq_00018__section1548114151414:

Check Item 6: Whether the Resource Limits Are Improperly Set for the Container
------------------------------------------------------------------------------

If the resource limits set for the container during workload creation are less than required, the container fails to be restarted.

.. code-block::

   Back-off restarting failed container

**Solution**

Modify the container specifications.

.. _cce_faq_00018__section17679197145618:

Check Item 7: Whether the Container Ports in the Same Pod Conflict with Each Other
----------------------------------------------------------------------------------

#. Log in to the node where the abnormal workload is located.

#. Check the ID of the container where the workload pod exits abnormally.

   **docker ps -a \| grep** *$podName*

#. View the logs of the corresponding container.

   **docker logs** *$containerID*

   Rectify the fault of the workload based on logs. As shown in the following figure, container ports in the same pod conflict. As a result, the container fails to be started.


   .. figure:: /_static/images/en-us_image_0000001178192674.png
      :alt: **Figure 2** Container restart failure due to a container port conflict

      **Figure 2** Container restart failure due to a container port conflict

**Solution**

Re-create the workload and set a port number that is not used by any other pod.

.. _cce_faq_00018__section1842111295128:

Check Item 8: Whether the Container Startup Command Is Correctly Configured
---------------------------------------------------------------------------

The error messages are as follows:

|image4|

**Solution**

Log in to the CCE console. On the workload details page, click **Upgrade** > **Advanced Settings** > **Lifecycle** to see whether the startup command is correctly configured.

.. |image1| image:: /_static/images/en-us_image_0000001223272341.png
.. |image2| image:: /_static/images/en-us_image_0000001223272343.png
.. |image3| image:: /_static/images/en-us_image_0000001177874154.png
.. |image4| image:: /_static/images/en-us_image_0000001178192672.png
