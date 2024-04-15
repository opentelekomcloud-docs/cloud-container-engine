:original_name: cce_faq_00152.html

.. _cce_faq_00152:

What Can I Do If an Error Is Reported When a Deployed Container Is Started After the JVM Startup Heap Memory Parameter Is Specified for ENTRYPOINT in Dockerfile?
=================================================================================================================================================================

Problem Description
-------------------

After the JVM startup heap memory parameter is specified for ENTRYPOINT in the Dockerfile, an error message "invalid initial heap size" is displayed during the deployed container's startup, as shown in the following figure:

|image1|

Answer
------

Check the ENTRYPOINT settings. The following settings are incorrect:

.. code-block::

   ENTRYPOINT ["java","-Xms2g -Xmx2g","-jar","xxx.jar"]

You can use either of the following methods to solve the problem:

-  **(Recommended)** Write the container startup command in **Workloads > Container Settings > Lifecycle > Startup Command**, then the container can be started properly.

-  Change the format of the **ENTRYPOINT** startup command to the following:

   .. code-block::

      ENTRYPOINT exec java -Xmx2g -Xms2g -jar xxxx.jar

.. |image1| image:: /_static/images/en-us_image_0000001797909393.png
