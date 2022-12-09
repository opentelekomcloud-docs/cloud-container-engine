:original_name: cce_faq_00210.html

.. _cce_faq_00210:

What Should I Do If Pods in the Terminating State Cannot Be Deleted?
====================================================================

Symptom
-------

When a node is in the Unavailable state, CCE migrates container pods on the node and sets the pods running on the node to the **Terminating** state.

After the node is restored, the pods in the **Terminating** state are automatically deleted.

Some pods are in the **Terminating** state occasionally.

|image1|

The pods cannot be deleted by running the **kubectl delete pods** command.

.. code-block::

   kubectl delete pods httpd-app-6df58645c6-cxgcm

Solution
--------

You can run the following command to forcibly delete the pods created in any ways:

.. code-block::

   kubectl delete pods <pod> --grace-period=0 --force

Therefore, you can run the following command to delete the pod **httpd-app-6df58645c6-cxgcm**:

.. code-block::

   kubectl delete pods httpd-app-6df58645c6-cxgcm --grace-period=0 --force

.. |image1| image:: /_static/images/en-us_image_0000001223473853.png
