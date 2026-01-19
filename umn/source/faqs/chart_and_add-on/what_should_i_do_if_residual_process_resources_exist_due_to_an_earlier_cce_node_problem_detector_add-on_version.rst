:original_name: cce_faq_00130.html

.. _cce_faq_00130:

What Should I Do If Residual Process Resources Exist Due to an Earlier CCE Node Problem Detector Add-on Version?
================================================================================================================

Description
-----------

When the node load is heavy, residual CCE Node Problem Detector process resources may exist.

Symptom
-------

After successful login to the ECS node where the CCE cluster runs, it is found that there are a large number of CCE Node Problem Detector processes exist.

|image1|

Solution
--------

Upgrade the CCE Node Problem Detector add-on to the latest version.

#. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose Add-ons, locate the CCE Node Problem Detector add-on, and click **Upgrade**.

   .. note::

      If the CCE Node Problem Detector add-on version is 1.13.6 or later, you do not need to upgrade it.

#. In the window that slides out from the right, configure the parameters and click **OK**.

.. |image1| image:: /_static/images/en-us_image_0000002516197799.png
