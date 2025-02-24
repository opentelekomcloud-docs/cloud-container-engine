:original_name: cce_10_0525.html

.. _cce_10_0525:

Rotation Certificates
=====================

Check Items
-----------

Check whether the number of certificates on your node is greater than 1000. During an upgrade, certificate files will be processed in batches. An excessive number of certificate files will lead to a slow node upgrade and result in pod eviction from the node.

Solution
--------

Solution 1 (preferred): Reset the node. For details, see :ref:`Resetting a Node <cce_10_0003>`.

Solution 2: Fix the certificate rotation issue on the node.

#. Go to the **/opt/cloud/cce/kubernetes/kubelet/pki/** directory on the node.

#. Back up certificate files **kubelet-server-current.pem** and **kubelet-client-current.pem** on the node.

#. Delete the residual **kubelet-server-\*** certificate files from the node.

   .. code-block::

      link_target="$(basename $(readlink kubelet-server-current.pem))" && find -maxdepth 1 -type f -name 'kubelet-server-*.pem' ! -name "$link_target" -delete

#. Delete soft links for the certificates.

   .. code-block::

      find -maxdepth 1 -type f -name 'kubelet-server-current.pem'  -delete
