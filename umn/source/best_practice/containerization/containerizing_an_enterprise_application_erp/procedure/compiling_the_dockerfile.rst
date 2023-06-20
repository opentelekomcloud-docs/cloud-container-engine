:original_name: cce_bestpractice_0008.html

.. _cce_bestpractice_0008:

Compiling the Dockerfile
========================

An image is the basis of a container. A container runs based on the content defined in the image. An image has multiple layers. Each layer includes the modifications made based on the previous layer.

Generally, Dockerfiles are used to customize images. Dockerfile is a text file and contains various instructions. Each instruction is used to build an image layer. That is, each instruction describes how to build an image layer.

This section describes how to compile a Dockerfile file.

.. note::

   Dockerfiles vary according to applications. Dockerfiles need to be compiled based on actual service requirements.

Procedure
---------

#. Log in as the **root** user to the device running Docker.

#. Write a Dockerfile.

   **vi Dockerfile**

   The following provides an example Dockerfile:

   .. code-block::

      # Centos:7.1.1503 is used as the base image.
      FROM centos:7.1.1503
      # Create a folder to store data and dependency files. You are advised to write multiple commands into one line to reduce the image size.
      RUN mkdir -p /usr/local/mongodb/data \
       && mkdir -p /usr/local/mongodb/bin \
       && mkdir -p /root/apache-tomcat-7.0.82 \
       && mkdir -p /root/jdk1.8.0_151

      # Copy the files in the apache-tomcat-7.0.82 directory to the container path.
      COPY ./apache-tomcat-7.0.82 /root/apache-tomcat-7.0.82
      # Copy the files in the jdk1.8.0_151 directory to the container path.
      COPY ./jdk1.8.0_151 /root/jdk1.8.0_151
      # Copy the files in the mongodb-linux-x86_64-rhel70-3.2.9 directory to the container path.
      COPY ./mongodb-linux-x86_64-rhel70-3.2.9/bin /usr/local/mongodb/bin
      # Copy start_tomcat_and_mongo.sh to the /root directory of the container.
      COPY ./start_tomcat_and_mongo.sh /root/

      # Enter Java environment variables.
      RUN chown root:root -R /root \
       && echo "JAVA_HOME=/root/jdk1.8.0_151 " >> /etc/profile  \
       && echo "PATH=\$JAVA_HOME/bin:$PATH " >> /etc/profile  \
       && echo "CLASSPATH=.:\$JAVA_HOME/lib/dt.jar:\$JAVA_HOME/lib/tools.jar" >> /etc/profile  \
       && chmod +x /root \
       && chmod +x /root/start_tomcat_and_mongo.sh

      # When the container is started, commands in start_tomcat_and_mongo.sh are automatically run. The file can be one or more commands, or a script.
      ENTRYPOINT ["/root/start_tomcat_and_mongo.sh"]

   In the preceding information:

   -  **FROM** statement: indicates that **centos:7.1.1503** is used as the base image.
   -  **Run** statement: indicates that a shell command is executed in the container.
   -  **Copy** statement: indicates that files in the local computer are copied to the container.
   -  **ENTRYPOINT** statement: indicates the commands that are run after the container is started.
