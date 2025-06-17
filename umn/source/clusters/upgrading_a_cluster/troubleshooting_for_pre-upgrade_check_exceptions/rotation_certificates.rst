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

#. Log in to the node and go to the node certificate directory.

   .. code-block::

      cd /opt/cloud/cce/kubernetes/kubelet/pki/

#. Delete the residual **kubelet-server-\*** certificate file and certificate soft link file from the node.

   .. code-block::

      find -maxdepth 1 -type f -name 'kubelet-server-*.pem' -delete
      rm -f ./kubelet-server-current.pem
