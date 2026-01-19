:original_name: cce_10_0522.html

.. _cce_10_0522:

Drainage Tasks
==============

Check Items
-----------

An unfinished drainage task is detected in the cluster, which may resume after the upgrade. If this happens, running pods will be evicted, which could impact your services.

Solutions
---------

#. Configure the **kubectl** command. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Check if there are any drainage tasks in progress.

   .. code-block::

      kubectl get drainage


   .. figure:: /_static/images/en-us_image_0000002516079095.png
      :alt: **Figure 1** An in-progress drainage task

      **Figure 1** An in-progress drainage task

   Delete the drainage resources and perform the pre-upgrade check again.

#. Run the following command to delete a drainage task:

   .. code-block::

      kubectl delete drainage {Name of the drainage task}
