:original_name: cce_10_0479.html

.. _cce_10_0479:

cce-hpa-controller Limitations
==============================

Check Items
-----------

Check whether there are compatibility limitations between the current and target cce-controller-hpa add-on versions.

Solutions
---------

There are compatibility limitations between the current and target versions of the cce-controller-hpa add-on. To address this, install an add-on that provides metrics APIs, like metrics-server, in the cluster.

Install the metrics add-on in the cluster and try again.

.. meta::
   :description: Resolve CCE HPA controller compatibility issues on T Cloud Public by installing the metrics-server add-on.
   :keywords: Cloud Container Engine, CCE, HPA, Add-on, Metrics Server
