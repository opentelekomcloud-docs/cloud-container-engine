:original_name: cce_faq_00319.html

.. _cce_faq_00319:

What Can I Do If a Layer Is Missing During Image Pull?
======================================================

Symptom
-------

When containerd is used as the container engine, there is a possibility that the image layer is missing when an image is pulled to a node. As a result, the workload container fails to be created.

|image1|

Possible Causes
---------------

Docker earlier than v1.10 supports the layer whose **mediaType** is **application/octet-stream**. However, containerd does not support **application/octet-stream**. As a result, the layer is not pulled.

Solution
--------

You can use either of the following methods to solve this problem:

-  Use Docker v1.11 or later to repackage the image.
-  Manually pull the image.

   #. Log in to the node.

   #. Run the following command to pull the image:

      **ctr -n k8s.io images pull --user u:p images**

   #. Use the newly pulled image to create a workload.

.. |image1| image:: /_static/images/en-us_image_0000001950316040.png
