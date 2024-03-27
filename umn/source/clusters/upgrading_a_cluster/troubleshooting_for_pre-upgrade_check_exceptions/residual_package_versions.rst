:original_name: cce_10_0505.html

.. _cce_10_0505:

Residual Package Versions
=========================

Check Items
-----------

Check whether there are residual package version data in the current cluster.

Solution
--------

A message is displayed indicating that there are residual 10.12.1.109 CRD resources in your cluster. This issue occurs because CRD resources are not cleared after nodes in earlier CCE versions are deleted.

Manually perform the following operations to clear the residual resources:

#. Back up the residual CRD resources. Take CRD resource 10.12.1.109 as an example. Replace it with the resource displayed in the error message.

   .. code-block::

      kubectl get packageversion 10.12.1.109 -oyaml > /tmp/packageversion-109.bak

#. Clear the residual CRD resources.

   .. code-block::

      kubectl delete packageversion 10.12.1.109

#. Check residual package versions again.
