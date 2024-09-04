:original_name: cce_bestpractice_0006.html

.. _cce_bestpractice_0006:

Preparing the Application Runtime
=================================

After application analysis, you have gained the understanding of the OS and runtime required for running the application. Make the following preparations:

-  :ref:`Installing Docker <cce_bestpractice_0006__section411319276259>`: During application containerization, build a container image. To do so, you have to prepare a PC and install Docker on it.
-  :ref:`Obtaining the base image tag <cce_bestpractice_0006__section7944139145718>`: Determine the base image based on the OS on which the application runs. In this example, the application runs on CentOS 7.1 and the base image can be obtained from an open-source image repository.
-  :ref:`Obtaining the runtime <cce_bestpractice_0006__section44401821348>`: Obtain the runtime of the application and the MongoDB database with which the application interconnects.

.. _cce_bestpractice_0006__section411319276259:

Installing Docker
-----------------

Docker is compatible with almost all operating systems. Select a Docker version that best suits your needs.

.. note::

   SWR uses Docker 1.11.2 or later to upload images.

   It is recommended that you install Docker and build images as the user **root**. Make sure to obtain the user **root** password for the host where Docker will be installed beforehand.

#. Log in as user **root** to the device on which Docker is about to be installed.

#. Quickly install Docker on the device running Linux. You can also manually install Docker. For details, see `Docker Engine installation <https://docs.docker.com/engine/install/#server>`__.

   **curl -fsSL get.docker.com -o get-docker.sh**

   **sh get-docker.sh**

#. Run the following command to check the Docker version:

   **docker version**

   .. code-block::

      Client:
      Version: 17.12.0-ce
      API Version:1.35
      ...

   **Version** indicates the version number.

.. _cce_bestpractice_0006__section7944139145718:

Obtaining the Base Image Tag
----------------------------

Determine the base image based on the OS on which the application runs. In this example, the application runs on CentOS 7.1 and the base image can be obtained from an open-source image repository.

.. note::

   Search for the image tag based on the OS on which the application runs.

#. Visit the Docker website.

#. Search for CentOS. The image corresponding to CentOS 7.1 is **centos7.1.1503**. Use this image name when editing the Dockerfile.


   .. figure:: /_static/images/en-us_image_0000001981435329.png
      :alt: **Figure 1** Obtaining the CentOS version

      **Figure 1** Obtaining the CentOS version

.. _cce_bestpractice_0006__section44401821348:

Obtaining the Runtime
---------------------

In this example, the web application of the Tomcat type is used. This application requires the runtime of Tomcat 7.0, and Tomcat requires JDK 1.8. In addition, the application must interconnect with the MongoDB database in advance.

.. note::

   Download the environment required by the application.

#. Download Tomcat, JDK, and MongoDB installation packages of the specific versions.

   a. Download JDK 1.8.

      Download address: https://www.oracle.com/java/technologies/jdk8-downloads.html.

   b. Download Tomcat 7.0 from http://archive.apache.org/dist/tomcat/tomcat-7/v7.0.82/bin/apache-tomcat-7.0.82.tar.gz.

   c. Download MongoDB 3.2 from https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-rhel70-3.2.9.tgz.

#. Log in as user **root** to the device running Docker.

#. Run the following commands to create the directory where the application is to be stored: For example, set the directory to **apptest**.

   **mkdir apptest**

   **cd apptest**

#. Use Xshell to save the downloaded dependency files to the **apptest** directory.

#. Run the following commands to decompress the dependency files:

   **tar -zxf apache-tomcat-7.0.82.tar.gz**

   **tar -zxf jdk-8u151-linux-x64.tar.gz**

   **tar -zxf mongodb-linux-x86_64-rhel70-3.2.9.tgz**

#. Save the enterprise application (for example, **apptest.war**) in the **webapps/apptest** directory of the Tomcat runtime environment.

   .. note::

      **apptest.war** is used as an example only. Use your own application for actual configuration.

   **mkdir -p apache-tomcat-7.0.82/webapps/app\ test**

   **cp apptest.war apache-tomcat-7.0.82/webapps/app\ test**

   **cd apache-tomcat-7.0.82/webapps/app\ test**

   **./../../../jdk1.8.0_151/bin/jar -xf apptest.war**

   **rm -rf apptest.war**
