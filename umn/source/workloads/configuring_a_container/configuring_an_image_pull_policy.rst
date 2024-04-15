:original_name: cce_10_0353.html

.. _cce_10_0353:

Configuring an Image Pull Policy
================================

When a workload is created, the container image is pulled from the image repository to the node. The image is also pulled when the workload is restarted or upgraded.

By default, **imagePullPolicy** is set to **IfNotPresent**, indicating that if the image exists on the node, the existing image is used. If the image does not exist on the node, the image is pulled from the image repository.

The image pull policy can also be set to **Always**, indicating that the image is pulled from the image repository and overwrites the image on the node regardless of whether the image exists on the node.

.. code-block::

   apiVersion: v1
   kind: Pod
   metadata:
     name: nginx
   spec:
     containers:
     - image: nginx:alpine
       name: container-0
       resources:
         limits:
           cpu: 100m
           memory: 200Mi
         requests:
           cpu: 100m
           memory: 200Mi
       imagePullPolicy: Always
     imagePullSecrets:
     - name: default-secret

An image pull policy can also be configured on the CCE console. When creating a workload, configure **Pull Policy**. If **Always** is selected, images are always pulled. If **Always** is not selected, images are pulled as needed.

.. important::

   Use a new tag each time you create an image. If you do not update the tag but only update the image, when **Pull Policy** is set to **IfNotPresent**, CCE considers that an image with the tag already exists on the current node and will not pull the image again.
