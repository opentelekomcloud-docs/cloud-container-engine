:original_name: cce_faq_00203.html

.. _cce_faq_00203:

Why Does the Browser Return Error Code 404 When I Access a Deployed Application?
================================================================================

CCE does not return any error code when you fail to access your applications using a browser. Check your services first.

**404 Not Found**

If the error code shown in the following figure is returned, it indicates that the ELB cannot find the corresponding forwarding policy. Check the forwarding policies.


.. figure:: /_static/images/en-us_image_0000001981275249.png
   :alt: **Figure 1** 404:ALB

   **Figure 1** 404:ALB

If the error code shown in the following figure is returned, it indicates that errors occur on Nginx (your services). In this case, check your services.


.. figure:: /_static/images/en-us_image_0000001981435093.png
   :alt: **Figure 2** 404:nginx/1.**.\*

   **Figure 2** 404:nginx/1.**.\*
