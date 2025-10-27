:original_name: cce_10_0518.html

.. _cce_10_0518:

Number of Node Images
=====================

Check Items
-----------

Check the number of images on your node. If there are more than 1000 images, it takes a long time for Docker to start, affecting the standard Docker output and functions such as Nginx.

Solution
--------

-  If there are too many images on a node, you can delete unnecessary images from the node.

   You can run the command below to delete an image with the **<none>** tag. Such images are typically left from builds or updates.

   -  For nodes that use Docker

      .. code-block::

         docker image prune

   -  For nodes that use containerd

      #. View all images with the **none** tags.

         .. code-block::

            crictl images | grep "<none>"

      #. Delete all images with the **none** tags in batches. Use **awk** to extract the image IDs and pipe them to **xargs** for deletion.

         .. code-block::

            crictl images | grep "<none>" | awk '{print $3}' | xargs -r crictl rmi

-  Too many images slow Docker or containerd startup on your nodes. This delays containers that burst stdout logs for just a few seconds typically. Skip this check if services are not affected.
