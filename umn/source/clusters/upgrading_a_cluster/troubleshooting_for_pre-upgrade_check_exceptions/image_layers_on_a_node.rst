:original_name: cce_10_0523.html

.. _cce_10_0523:

Image Layers on a Node
======================

Check Items
-----------

Check the number of image layers on your node. If there are more than 5000 layers, it will take a long time for Docker or containerd to start, affecting the stdout of Docker or containerd.

If Nginx is used in your cluster, data forwarding may be slow.

Solution
--------

Log in to the node and manually delete unnecessary images.
