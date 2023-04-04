:original_name: cce_10_0175.html

.. _cce_10_0175:

Obtaining a Cluster Certificate
===============================

Scenario
--------

This section describes how to obtain the cluster certificate from the console and use it to access Kubernetes clusters.

Procedure
---------

#. Log in to the CCE console and access the cluster console.

#. Choose **Cluster Information** from the navigation pane and click **Download** next to **Authentication Mode** in the **Connection Information** area.

#. In the **Download X.509 Certificate** dialog box displayed, select the certificate expiration time and download the X.509 certificate of the cluster as prompted.


   .. figure:: /_static/images/en-us_image_0000001199181228.png
      :alt: **Figure 1** Downloading a certificate

      **Figure 1** Downloading a certificate

   .. important::

      -  The downloaded certificate contains three files: **client.key**, **client.crt**, and **ca.crt**. Keep these files secure.
      -  Certificates are not required for mutual access between containers in a cluster.
