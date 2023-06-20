:original_name: cce_10_0487.html

.. _cce_10_0487:

Checking Deprecated Kubernetes APIs
===================================

Check Item
----------

The system scans the audit logs of the past day to check whether the user calls the deprecated APIs of the target Kubernetes version.

.. note::

   Due to the limited time range of audit logs, this check item is only an auxiliary method. APIs to be deprecated may have been used in the cluster, but their usage is not included in the audit logs of the past day. Check the API usage carefully.

Solution
--------

**Description**

The check result shows that your cluster calls a deprecated API of the target cluster version through kubectl or other applications. You can rectify the fault before the upgrade. Otherwise, the API will be intercepted by kube-apiserver after the upgrade. For details about each deprecated API, see `Deprecated API Migration Guide <https://kubernetes.io/docs/reference/using-api/deprecation-guide/>`__.

**Cases**

Ingresses of extensions/v1beta1 and networking.k8s.io/v1beta1 API are deprecated in clusters of v1.22. If you upgrade a CCE cluster from v1.19 or v1.21 to v1.23, existing resources are not affected, but the v1beta1 API version may be intercepted in the creation and editing scenarios.

For details about the YAML configuration structure changes, see :ref:`Using kubectl to Create an ELB Ingress <cce_10_0252>`.
