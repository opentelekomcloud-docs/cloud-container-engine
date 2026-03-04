:original_name: cce_bestpractice_0009.html

.. _cce_bestpractice_0009:

Building and Uploading an Image
===============================

This section describes how to build an entire application into a Docker image. After building an image, you can use the image to deploy and upgrade the application. This reduces manual configuration and improves efficiency.

.. note::

   When building an image, ensure that files used to build the image are stored in the same directory.

Required Cloud Services
-----------------------

SoftWare Repository for Container (SWR) provides easy, secure, and reliable management over container images throughout their lifecycle, facilitating the deployment of containerized services.

Basic Concepts
--------------

-  Image: A Docker image is a special file system that includes everything needed to run containers: programs, libraries, resources, settings, and so on. It also includes corresponding configuration parameters (such as anonymous volumes, environment variables, and users) required within a container runtime. An image does not contain any dynamic data, and its content remains unchanged after being built.
-  Container: Images become containers at runtime, that is, containers are created from images. A container can be created, started, stopped, deleted, or suspended.

Procedure
---------

#. Log in as user **root** to the device running Docker.

#. Enter the **apptest** directory.

   **cd apptest**

   Ensure that files used to build the image are stored in the same directory.

   |image1|

#. Build an image.

   **docker build -t apptest:v1 .**

#. Upload the image to SWR. For details, see `Uploading an Image Through the Client <https://docs.otc.t-systems.com/usermanual/swr/swr_01_0011.html>`__.

.. |image1| image:: /_static/images/en-us_image_0000002516197739.png
