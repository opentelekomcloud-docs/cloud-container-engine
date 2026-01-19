:original_name: cce_10_1027.html

.. _cce_10_1027:

Switching the AOM Instance Connected to Grafana
===============================================

After connecting to an AOM instance, Grafana creates a data source named **prometheus-aom** and adds it to data sources through Provisioning, an automatic configuration method provided by Grafana. Provisioning allows you to automate the setup of data sources, dashboards, and alert rules using configuration files.

The configuration for connecting to an AOM instance contains authentication credentials. If you directly switch the AOM instance connected to Grafana, the Grafana security mechanism will be triggered, and the operation will be denied. This section describes how you can switch the AOM instance connected to Grafana.


Switching the AOM Instance Connected to Grafana
-----------------------------------------------

#. Log in to Grafana and choose **Data Sources** from the navigation pane.

   |image1|

#. Click the **prometheus-aom** data source in the list on the right and delete it.

   |image2|

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Add-ons**. On the displayed page, locate **Grafana** and click **Edit**.

#. Select the AOM instance to be switched and click **OK**.

.. |image1| image:: /_static/images/en-us_image_0000002516079275.png
.. |image2| image:: /_static/images/en-us_image_0000002516199271.png
