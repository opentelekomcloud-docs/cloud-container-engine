:original_name: cce_10_0602.html

.. _cce_10_0602:

Enabling Overload Control for a Cluster
=======================================

Cluster overload occurs when system load such as request volume or resource usage exceeds the system's processing capacity, leading to degraded performance or system failure. Overload control is a dynamic mechanism that limits concurrent external requests based on the resource pressure of the master node, ensuring the stability and reliability of the master node and the cluster. In a CCE standard or Turbo cluster, you can enable cluster overload control, monitor cluster overload, and configure overload alarms to reduce the risk of cluster overload. For details, see :ref:`Figure 1 <cce_10_0602__fig548881275015>`.

.. _cce_10_0602__fig548881275015:

.. figure:: /_static/images/en-us_image_0000002253619705.png
   :alt: **Figure 1** Process of cluster overload control

   **Figure 1** Process of cluster overload control

Notes and Constraints
---------------------

The cluster version must be 1.23 or later.

Enabling Overload Control
-------------------------

After overload control is enabled, CCE will proportionally reject external traffic based on the overload level when the master node is overloaded. This effectively reduces the load on the master node and ensures the stability and availability of the cluster.

**Method 1: Enable overload control when creating a cluster.**

#. When creating a cluster of v1.23 or later, enable **Overload Control** in **(Optional) Advanced Settings > Cluster Scalability** on the **Buy Cluster** page. After this function is enabled, CCE will dynamically adjust concurrent requests based on the master node's resource pressure to ensure the stability and reliability of the master node and the cluster.
#. After the cluster is created, click the cluster name. The cluster **Overview** page is displayed. In the **Master Nodes** area at the lower right of the page, overload control has been enabled if the following information is displayed: Overload control enabled. An overloaded master node rejects external traffic proportionally based on the overload level.

**Method 2: Enable overload control in an existing cluster.**

#. Log in to the CCE console and click the name of the cluster of v1.23 or later. The cluster **Overview** page is displayed.
#. In the **Master Nodes** area at the lower right of the page, click **Enable**.
#. In the **Master Nodes** area, overload control has been enabled if the following information is displayed: Overload control enabled. An overloaded master node rejects external traffic proportionally based on the overload level.

Overload Monitoring
-------------------

You can monitor cluster metrics to promptly check the cluster overload status.

#. Log in to the CCE console and click the name of an existing cluster whose version is v1.23 or later.

#. On the **Overview** page, check the master node information. The overload level metric will be displayed.

   The overload levels are as follows:

   -  Circuit breaking: Rejects all external traffic.
   -  Severe overload: Rejects 75% external traffic.
   -  Moderate overload: Rejects 50% external traffic.
   -  Slight overload: Rejects 25% external traffic.
   -  Normal: Does not reject external traffic.

Disabling Cluster Overload Control
----------------------------------

You can disable overload control when it is no longer needed. After overload control is disabled, the master node will no longer reject external traffic when overloaded. Exercise caution when performing this operation.

#. Log in to the CCE console and click the name of an existing cluster whose version is v1.23 or later.
#. In the navigation pane, choose **Settings**.
#. On the **Cluster Access** tab page, disable overload control.
#. Click **OK**.
