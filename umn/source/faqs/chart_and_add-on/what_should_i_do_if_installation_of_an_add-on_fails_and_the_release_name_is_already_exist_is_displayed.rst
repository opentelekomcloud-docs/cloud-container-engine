:original_name: cce_faq_00322.html

.. _cce_faq_00322:

What Should I Do If Installation of an Add-on Fails and "The release name is already exist" Is Displayed?
=========================================================================================================

Symptom
-------

When an add-on fails to be installed, the error message "The release name is already exist" is returned.

Possible Cause
--------------

The add-on release record remains in the Kubernetes cluster. Generally, it is because the cluster etcd has backed up and restored the add-on, or the add-on fails to be installed or deleted.

Solution
--------

Use kubectl to connect to the cluster and manually clear the Secret and ConfigMap corresponding to the add-on release. The following uses autoscaler add-on release as an example.

#. Connect to the cluster using kubectl, and run the following command to view the Secret list of add-on releases:

   **kubectl get secret -A \|grep cceaddon**

   |image1|

   The Secret name of an add-on release is in the format of **sh.helm.release.v1.cceaddon-{add-on name}.v\***. If there are multiple release versions, you can delete their Secrets at the same time.

#. Run the **release secret** command to delete the Secrets.

   Example:

   **kubectl delete secret sh.helm.release.v1.cceaddon-autoscaler.v1 sh.helm.release.v1.cceaddon-autoscaler.v2 -nkube-system**

   |image2|

#. If the add-on is created when Helm v2 is used, CCE automatically bumps the v2 release in ConfigMaps to v3 release in Secrets when viewing the add-ons and their details. The v2 release in the original ConfigMap is not deleted. Run the following command to view the ConfigMap list of add-on releases:

   **kubectl get configmap -A \| grep cceaddon**

   |image3|

   The ConfigMap name of an add-on release is in the format of **cceaddon-{add-on name}.v\***. If there are multiple release versions, you can delete their ConfigMaps at the same time.

#. Run the **release configmap** command to delete the ConfigMaps.

   Example:

   **kubectl delete configmap cceaddon-autoscaler.v1 cceaddon-autoscaler.v2 -nkube-system**

   |image4|

   .. caution::

      Deleting resources in kube-system is a high-risk operation. Ensure that the command is correct before running it to prevent resources from being deleted by mistake.

#. On the CCE console, install the add-on and then uninstall it. Ensure that the residual add-on resources are cleared. After the uninstallation is complete, install the add-on again.

   .. note::

      During the initial installation of the add-on, it is possible to encounter abnormal behavior caused by residual resources from a previous add-on release. This is a normal occurrence. In such cases, you can resolve the issue by uninstalling the add-on from the console. This will ensure that any remaining resources are cleared, allowing for a proper installation of the add-on again.

.. |image1| image:: /_static/images/en-us_image_0000002218818994.png
.. |image2| image:: /_static/images/en-us_image_0000002253778813.png
.. |image3| image:: /_static/images/en-us_image_0000002253618897.png
.. |image4| image:: /_static/images/en-us_image_0000002218659174.png
