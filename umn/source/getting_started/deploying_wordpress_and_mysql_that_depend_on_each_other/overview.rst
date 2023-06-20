:original_name: cce_qs_0009.html

.. _cce_qs_0009:

Overview
========

WordPress was originally a blog platform based on PHP and MySQL. It is gradually evolved into a content management system. You can set up your own blog website on any server that supports PHP and MySQL. Thousands of plug-ins and countless theme templates are available for WordPress and easy to install.

WordPress is a blog platform developed in hypertext preprocessor (PHP). You can set up your websites on the services that support PHP and MySQL databases, or use WordPress as a content management system. For more information about WordPress, visit https://wordpress.org/.

WordPress must be used together with MySQL. WordPress runs the content management program while MySQL serves as a database to store data. Generally, WordPress and MySQL run in different containers, as shown in the following figure.


.. figure:: /_static/images/en-us_image_0000001550678257.png
   :alt: **Figure 1** WordPress

   **Figure 1** WordPress

In this example, two container images are involved.

-  `WordPress <https://github.com/docker-library/docs/tree/master/wordpress>`__: Select wordpress:php7.3 in this example.
-  `MySQL <https://github.com/docker-library/docs/tree/master/mysql>`__: Select mysql:5.7 in this example.

When WordPress accesses MySQL in a cluster, Kubernetes provides a resource object called Service for the workload access. In this example, a Service is created for MySQL and WordPress, respectively. For details about how to create and configure a Service, see the following sections.
