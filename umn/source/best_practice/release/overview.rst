:original_name: cce_bestpractice_10001.html

.. _cce_bestpractice_10001:

Overview
========

Background
----------

When switching between old and new services, you may be challenged in ensuring the system service continuity. If a new service version is directly released to all users at a time, it can be risky because once an online accident or bug occurs, the impact on users is great. It could take a long time to fix the issue. Sometimes, the version has to be rolled back, which severely affects user experience.

Solution
--------

Several release policies are developed for service upgrades, including grayscale releases, blue-green deployment, A/B testing, rolling upgrades, and batch suspension of releases. Traffic loss or service unavailability caused by releases can be avoided as much as possible.

This section describes the principles and practices of grayscale releases and blue-green deployment.

-  Grayscale releases, also called canary releases, are a smooth iteration mode for version upgrades. During the upgrades, some users use the new version, while other users continue to use the old version. After the new version is stable and ready, it gradually takes over all the live traffic. In this way, service risks brought by the release of the new version can be minimized, the impact of faults can be reduced, and quick rollbacks are supported.

   The figure below shows the general process of a grayscale release. First, divide 20% of all service traffic to the new version. If the service version runs normally, gradually increase the traffic proportion and continue to test the performance of the new version. If the new version is stable, switch all traffic to it and bring the old version offline.

   |image1|

   |image2|

   |image3|

   If an exception occurs in the new version when 20% of the traffic goes to it, you can quickly switch back to the old version.

   |image4|

   |image5|

   |image6|

   |image7|

-  Blue-green deployment provides a zero-downtime, predictable manner for releasing applications to reduce service interruption during the release. A new version is deployed while the old version is retained. The two versions are online concurrently. The new and old versions work in hot backup mode. The route weight is switched (0 or 100) to enable different versions to go online or offline. If a problem occurs, the version can be quickly rolled back.

   |image8|

   |image9|

   |image10|

   |image11|

.. |image1| image:: /_static/images/en-us_image_0000002662010407.png
.. |image2| image:: /_static/images/en-us_image_0000002631691164.png
.. |image3| image:: /_static/images/en-us_image_0000002631531288.png
.. |image4| image:: /_static/images/en-us_image_0000002661850459.png
.. |image5| image:: /_static/images/en-us_image_0000002662010399.png
.. |image6| image:: /_static/images/en-us_image_0000002662010415.png
.. |image7| image:: /_static/images/en-us_image_0000002631531268.png
.. |image8| image:: /_static/images/en-us_image_0000002631691182.png
.. |image9| image:: /_static/images/en-us_image_0000002662010411.png
.. |image10| image:: /_static/images/en-us_image_0000002631531264.png
.. |image11| image:: /_static/images/en-us_image_0000002631531284.png
