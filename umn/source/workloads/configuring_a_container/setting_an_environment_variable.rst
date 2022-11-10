:original_name: cce_01_0113.html

.. _cce_01_0113:

Setting an Environment Variable
===============================

Scenario
--------

An environment variable is a variable whose value can affect the way a running container will behave. You can modify environment variables even after workloads are deployed, increasing flexibility in workload configuration.

The function of setting environment variables on CCE is the same as that of specifying **ENV** in a Dockerfile.

CCE provides three ways to add environment variables: Manually add environment variables, import environment variables from a secret, and import environment variables from a configMap.

.. important::

   After a container is started, do not modify configurations in the container. If configurations in the container are modified (for example, passwords, certificates, and environment variables of a containerized application are added to the container), the configurations will be lost after the container restarts and container services will become abnormal. An example scenario of container restart is pod rescheduling due to node anomalies.

   Configurations must be imported to a container as arguments. Otherwise, configurations will be lost after the container restarts.

Manually Adding Environment Variables
-------------------------------------

#. When creating a workload, add a container image. Then, expand **Environment Variables** and click **Add Environment Variables**.

#. Configure the following parameters as required:

   -  **Type**: Set this to **Added manually**.
   -  **Variable Name**: Enter a variable name, for example, demo.
   -  **Variable Value/Reference**: Enter a variable value, for example, value.


   .. figure:: /_static/images/en-us_image_0000001190302095.png
      :alt: **Figure 1** Manually adding environment variables

      **Figure 1** Manually adding environment variables

Importing Environment Variables from a Secret
---------------------------------------------

#. You need to create a key first. For details, see :ref:`Creating a Secret <cce_01_0153>`.

#. When creating a workload, add a container image. Then, expand **Environment Variables** and click **Add Environment Variables**.

#. Configure the following parameters as required:

   -  **Type**: Set this to **Added from Secret**.
   -  **Variable Name**: Enter a variable name.
   -  **Variable Value/Reference**: Select the corresponding secret name and key.


   .. figure:: /_static/images/en-us_image_0000001190302097.png
      :alt: **Figure 2** Importing environment variables from a secret

      **Figure 2** Importing environment variables from a secret

Importing Environment Variables from a ConfigMap
------------------------------------------------

#. Create a ConfigMap first. For details, see :ref:`Creating a ConfigMap <cce_01_0152>`.
#. When creating a workload, add a container image. Then, expand **Environment Variables** and click **Add Environment Variables**.
#. Configure the following parameters as required:

   -  **Type**: Set this to **Added from ConfigMap**.
   -  **Variable Name**: Enter a variable name.
   -  **Variable Value/Reference**: Select the corresponding ConfigMap name and key.
