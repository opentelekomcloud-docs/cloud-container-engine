:original_name: cce_faq_00418.html

.. _cce_faq_00418:

Why Cannot I Delete a PV or PVC Using the kubectl delete Command?
=================================================================

Symptom
-------

An existing PV or PVC cannot be deleted by running the **kubectl delete** command and it remains in the terminating state.

Possible Cause
--------------

To prevent data loss caused by mis-deletion of PVs or PVCs, Kubernetes provides a data protection mechanism. A PV or PVC cannot be directly deleted using the **kubectl delete** command.

Solution
--------

Run the **kubectl patch** command first to remove the protection mechanism and then delete the PV or PVC.

If you have run **kubectl delete** to delete a PV or PVC, the PV or PVC remains in the terminating state. It will be directly deleted after you run the **kubectl patch** command.

-  Run the following command to delete a PV:

   .. code-block::

      kubectl patch pv <pv-name> -p '{"metadata":{"finalizers":null}}'
      kubectl delete pv <pv-name>

-  Run the following command to delete a PVC:

   .. code-block::

      kubectl patch pvc <pvc-name> -p '{"metadata":{"finalizers":null}}'
      kubectl delete pvc <pvc-name>
