:original_name: cce_faq_00199.html

.. _cce_faq_00199:

What Is the Image Pull Policy for Containers in a CCE Cluster?
==============================================================

A container image is required to create a container. Images may be stored locally or in a remote image repository.

The **imagePullPolicy** field in the Kubernetes configuration file is used to describe the image pull policy. This field has the following value options:

-  **Always**: Always force a pull.

   .. code-block::

      imagePullPolicy: Always

-  **IfNotPresent**: The image is pulled only if it is not already present locally.

   .. code-block::

      imagePullPolicy: IfNotPresent

-  **Never**: The image is assumed to exist locally. No attempt is made to pull the image.

   .. code-block::

      imagePullPolicy: Never

**Description**

#. If this field is set to **Always**, the image is pulled from the remote repository each time a container is started or restarted.

   If **imagePullPolicy** is left blank, the policy defaults to **Always**.

#. If the policy is set to **IfNotPreset**:

   a. If the required image does not exist locally, it will be pulled from the remote repository.
   b. If the content, except the tag, of the required image is the same as that of the local image, and the image with that tag exists only in the remote repository, Kubernetes will not pull the image from the remote repository.
