:original_name: cce_10_0205.html

.. _cce_10_0205:

metrics-server
==============

From version 1.8 onwards, Kubernetes provides resource usage metrics, such as the container CPU and memory usage, through the Metrics API. These metrics can be directly accessed by users (for example, by using the **kubectl top** command) or used by controllers (for example, Horizontal Pod Autoscaler) in a cluster for decision-making. The specific component is metrics-server, which is used to substitute for heapster for providing the similar functions. heapster has been gradually abandoned since v1.11.

metrics-server is an aggregator for monitoring data of core cluster resources. You can quickly install this add-on on the CCE console.

After metrics-server is installed, you can create an HPA policy on the **Workload Scaling** tab page of the **Auto Scaling** page. For details, see :ref:`Creating an HPA Policy for Workload Auto Scaling <cce_10_0208>`.

The official community project and documentation are available at https://github.com/kubernetes-sigs/metrics-server.

Installing the Add-on
---------------------

#. Log in to the CCE console and access the cluster console. Choose **Add-ons** in the navigation pane, locate **metrics-server** on the right, and click **Install**.
#. Select **Single** or **HA** for **Add-on Specifications**, and click **Install**.
