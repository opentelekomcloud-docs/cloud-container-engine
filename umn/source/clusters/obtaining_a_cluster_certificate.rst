:original_name: cce_01_0175.html

.. _cce_01_0175:

Obtaining a Cluster Certificate
===============================

Scenario
--------

Before accessing cluster resources through open-source Kubernetes APIs, obtain the cluster's certificate.

Procedure
---------

#. Log in to the CCE console. In the navigation pane, choose **Resource Management** > **Clusters**.

#. In the card view of the target cluster, choose **More** > **Download X.509 Certificate**.

#. In the **Download X.509 Certificate** dialog box displayed, select the certificate expiration time and download the X.509 certificate of the cluster as prompted.


   .. figure:: /_static/images/en-us_image_0000001190859184.png
      :alt: **Figure 1** Downloading a certificate

      **Figure 1** Downloading a certificate

   .. important::

      -  The downloaded certificate contains three files: **client.key**, **client.crt**, and **ca.crt**. Keep these files secure.
      -  Certificates are not required for mutual access between containers in a cluster.
