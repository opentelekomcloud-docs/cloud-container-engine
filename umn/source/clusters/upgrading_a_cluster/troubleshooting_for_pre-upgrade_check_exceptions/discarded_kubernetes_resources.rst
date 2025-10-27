:original_name: cce_10_0440.html

.. _cce_10_0440:

Discarded Kubernetes Resources
==============================

Check Items
-----------

Check whether there are discarded resources in the clusters.

Solution
--------

-  **Scenario 1: The Service in the clusters of v1.25 or later has deprecated annotation tolerate-unready-endpoints.**

   Error log:

   .. code-block::

      some check failed in cluster upgrade: this cluster has deprecated service list: map[test-svc] with deprecated annotation list [tolerate-unready-endpoints]

   In this example, check the log to see if the Service (**test-svc**) has the **tolerate-unready-endpoints** annotation. This annotation indicates whether the endpoint controller should continue creating endpoints for unready pods. If the deprecated annotation exists, delete it and add the following field to the Service's **spec** as a replacement. For details, see `ServiceSpec <https://kubernetes.io/docs/reference/kubernetes-api/service-resources/service-v1/#ServiceSpec>`__.

   #. Run the kubectl command to modify the YAML file of the Service.

      .. code-block::

         kubectl edit service test-svc

   #. Delete the **"tolerate-unready-endpoints"** annotation and add the following field to **spec** to replace the annotation:

      .. code-block::

         publishNotReadyAddresses: true

      |image1|

-  **Scenario 2: The Service in the clusters of v1.27 or later has deprecated annotation service.kubernetes.io/topology-aware-hints.**

   Error log:

   .. code-block::

      some check failed in cluster upgrade: this cluster has deprecated service list: map[test-svc] with deprecated annotation list [service.kubernetes.io/topology-aware-hints]

   In this example, check the log to see if the Service (**test-svc**) contains the **service.kubernetes.io/topology-aware-hints** annotation. This annotation was used to enable topology aware hints in Services. However, this function have been renamed. If the deprecated annotation exists, delete it and use **service.kubernetes.io/topology-mode** as a replacement (For details, see `Topology Aware Routing <https://kubernetes.io/docs/concepts/services-networking/topology-aware-routing/>`__):

   #. Run the kubectl command to modify the YAML file of the Service.

      .. code-block::

         kubectl edit service test-svc

   #. Delete the **"service.kubernetes.io/topology-aware-hints"** annotation and add the **"service.kubernetes.io/topology-mode"** annotation to **spec**.

      |image2|

.. |image1| image:: /_static/images/en-us_image_0000002434080064.png
.. |image2| image:: /_static/images/en-us_image_0000002467718549.png
