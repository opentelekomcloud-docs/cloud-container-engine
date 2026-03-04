:original_name: cce_10_0554.html

.. _cce_10_0554:

Collecting Control Plane Component Logs
=======================================

CCE allows you to collect the logs of master nodes. On the **Logging** page, you can select one or more control plane components (kube-controller-manager, kube-apiserver, and kube-scheduler) whose logs need to be reported.

Constraints
-----------

-  The cluster version must be v1.21.7-r0 or later, v1.23.5-r0 or later, or 1.25.
-  There is required LTS resource quota.

.. _cce_10_0554__section133166619593:

Control Plane Components
------------------------

CCE can collect the following types of control plane logs. Each log stream corresponds to a component of the Kubernetes control plane. To learn more about these components, see `Kubernetes Components <https://kubernetes.io/docs/concepts/overview/components/>`__.

.. table:: **Table 1** Control plane components

   +-------------------------+---------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Component               | Log Stream                            | Description                                                                                                                                                                                                                        |
   +=========================+=======================================+====================================================================================================================================================================================================================================+
   | kube-apiserver          | kube-apiserver-{{clusterID}}          | Exposes Kubernetes APIs. For more information, see `kube-apiserver <https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/>`__.                                                                         |
   +-------------------------+---------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | kube-controller-manager | kube-controller-manager-{{clusterID}} | Manages controllers and embeds the core control loops shipped with Kubernetes. For more information, see `kube-controller-manager <https://kubernetes.io/docs/reference/command-line-tools-reference/kube-controller-manager/>`__. |
   +-------------------------+---------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | kube-scheduler          | kube-scheduler-{{clusterID}}          | The default scheduler of a Kubernetes cluster. For more information, see `kube-scheduler <https://kubernetes.io/docs/reference/command-line-tools-reference/kube-scheduler/>`__.                                                   |
   +-------------------------+---------------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Enabling Control Plane Logging
------------------------------

**Enabling control plane logging during cluster creation**

#. Log in to the CCE console.
#. Click **Create Cluster**. Then, configure the parameters and click **Next: Select Add-on**.
#. On the displayed page, select **Cloud Native Log Collection** and click **Next: Configure Add-on**.
#. On the displayed page, select **Control Plane Logs** for **Cloud Native Log Collection**.
#. Click **Next: Confirm Settings**.

**Enabling control plane logging for an existing cluster**

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Logging**.

#. Click the **Control Plane Logs** tab and modify the settings in **Logging Settings**.


   .. figure:: /_static/images/en-us_image_0000002484119292.png
      :alt: **Figure 1** Modifying logging settings

      **Figure 1** Modifying logging settings

#. Determine whether to enable logging for each component. If yes, click |image1|.

Viewing Control Plane Component Logs of the Target Cluster
----------------------------------------------------------

**Viewing control plane component logs of the target cluster on the CCE console**

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Logging**.

#. Click the **Control Plane Logs** tab and select the topic of logs to be viewed. For details about available control plane log types, see :ref:`Control Plane Components <cce_10_0554__section133166619593>`.

   By default, no field index is configured for log streams created for CCE. You can choose **Configure Index** > **Index Settings** > **Index Fields** > **Auto Configure** to quickly configure field indexes. You can add fields extracted from structured logs to collect and analyze statistics.

**Viewing control plane component logs of the target cluster on the LTS console**

#. Log in to the LTS console and choose **Log Management**.

#. Search for the log group by cluster ID and click the log group name to view the log streams.

   By default, no field index is configured for log streams created for CCE. You can choose **Configure Index** > **Index Settings** > **Index Fields** > **Auto Configure** to quickly configure field indexes. You can add fields extracted from structured logs to collect and analyze statistics.

Disabling Control Plane Logging
-------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Logging**.
#. Click the **Control Plane Logs** tab. Then, modify the log setting.
#. Determine whether to enable logging for each component.If you enable logging, click |image2|.

   .. note::

      After you disable control plane logging, logs are no longer written to the original log stream, but the existing logs will not be deleted and expenses may be incurred for this.

.. |image1| image:: /_static/images/en-us_image_0000002484119302.png
.. |image2| image:: /_static/images/en-us_image_0000002516199277.png
