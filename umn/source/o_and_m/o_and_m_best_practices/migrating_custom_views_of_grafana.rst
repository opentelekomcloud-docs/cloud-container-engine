:original_name: cce_10_0960.html

.. _cce_10_0960:

Migrating Custom Views of Grafana
=================================

In Grafana v1.3.1 and later versions, open-source Grafana versions v10 and v7 are available. However, if you upgrade the open-source Grafana version from v7.\ *x* to v10.\ *x*, custom views may be incompatible. In this case, you need to uninstall Grafana and reinstall it using open-source version v10. If there are custom views, perform the following operations to migrate them.

.. _cce_10_0960__section1769007191514:

Step 1: Export Custom Views from Grafana of a Version Earlier Than 1.3.1
------------------------------------------------------------------------

#. Log in to the Grafana console. In the navigation pane, choose **Dashboards** > **Manage**. Then, click the view name to go to the details page.

   |image1|

#. In the upper left corner, click **Share dashboard or panel**.

   |image2|

#. Click the **Export** tab and click **Save to file**.

   |image3|

#. Repeat the preceding steps to export all custom views.

Step 2: Uninstall and Reinstall Grafana
---------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Add-ons**. On the displayed page, locate the installed **Grafana** add-on and click **Uninstall**.
#. Reinstall the add-on. Select **v10** for **Open-Source Community Version**. For more parameters, see :ref:`Grafana <cce_10_0828>`.
#. Click **Install**.

Step 3: Import Custom Views
---------------------------

#. Log in to Grafana of the new version and go to the **Dashboards** page.

   |image4|

#. Click **Import** on the right.

   |image5|

#. Upload a custom view exported in :ref:`Step 1: Export Custom Views from Grafana of a Version Earlier Than 1.3.1 <cce_10_0960__section1769007191514>`.

   |image6|

#. Click **Import**.

   |image7|

#. Check whether the view is normal and repeat the preceding steps to import all custom views.

.. |image1| image:: /_static/images/en-us_image_0000002483959474.png
.. |image2| image:: /_static/images/en-us_image_0000002516079421.png
.. |image3| image:: /_static/images/en-us_image_0000002484119448.png
.. |image4| image:: /_static/images/en-us_image_0000002516199429.png
.. |image5| image:: /_static/images/en-us_image_0000002483959478.png
.. |image6| image:: /_static/images/en-us_image_0000002516079429.png
.. |image7| image:: /_static/images/en-us_image_0000002516199425.png
