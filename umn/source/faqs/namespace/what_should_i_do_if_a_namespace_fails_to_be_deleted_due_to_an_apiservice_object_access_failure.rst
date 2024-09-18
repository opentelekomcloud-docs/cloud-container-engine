:original_name: cce_faq_00325.html

.. _cce_faq_00325:

What Should I Do If a Namespace Fails to Be Deleted Due to an APIService Object Access Failure?
===============================================================================================

Symptom
-------

The namespace remains in the Deleting state. The error message "DiscoveryFailed" is displayed in **status** in the YAML file.

|image1|

In the preceding figure, the full error message is "Discovery failed for some groups, 1 failing: unable to retrieve the complete list of server APIs: metrics.k8s.io/v1beta1: the server is currently unable to handle the request".

This indicates that the namespace deletion is blocked when kube-apiserver accesses the APIService resource object of the metrics.k8s.io/v1beta1 API.

Possible Causes
---------------

If an APIService object exists in the cluster, deleting the namespace will first access the APIService object. If the access fails, the namespace deletion will be blocked. In addition to the APIService objects created by users, add-ons like metrics-server and prometheus in the CCE cluster automatically create APIService objects.

.. note::

   For details, see https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/apiserver-aggregation/.

Solution
--------

Use either of the following methods:

-  Rectify the APIService object in the error message. If the object is created by an add-on, ensure that the pod where the add-on locates is running properly.
-  Delete the APIService object in the error message. If the object is created by an add-on, uninstall the add-on.

.. |image1| image:: /_static/images/en-us_image_0000001981275621.png
