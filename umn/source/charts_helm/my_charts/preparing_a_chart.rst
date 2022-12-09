:original_name: cce_01_0144.html

.. _cce_01_0144:

Preparing a Chart
=================

You can prepare a chart using one of the following methods:

-  :ref:`Customizing a Chart <cce_01_0144__s84a75de063eb4fb29387e64d133b0da6>`
-  :ref:`Using a Kubernetes Official Chart <cce_01_0144__s5f9699b10586401d81cfebd947cf416f>`

.. _cce_01_0144__s84a75de063eb4fb29387e64d133b0da6:

Customizing a Chart
-------------------

#. Customize the content of a chart as required.

   For details about how to create a chart, see https://helm.sh/docs/chart_template_guide/getting_started/.

#. Set the chart directory structure and name the chart based on the requirements defined in :ref:`Chart Specifications <cce_01_0144__s8af9336c49a44399865c7a0f3149d789>`.

.. _cce_01_0144__s5f9699b10586401d81cfebd947cf416f:

Using a Kubernetes Official Chart
---------------------------------

#. .. _cce_01_0144__l6d35ccf85da74660b802f524cc9e3095:

   Visit https://artifacthub.io/ to obtain the required chart.

#. Log in to a Linux host.

#. Upload the chart obtained in :ref:`1 <cce_01_0144__l6d35ccf85da74660b802f524cc9e3095>`.

#. Run the following command to compress the chart.

   -  If the Helm client is not installed on the Linux host, run the following command:

      **tar pzcf {name}-{version}.tgz {name}/**

      In the preceding command,

      **{name}** indicates the actual chart name.

      **{version}** indicates the actual chart version.

      .. important::

         The values of {name} and {version} must be the same as the values of name and version in the **Chart.yaml** file in the chart.

   -  If the Helm client is installed on the Linux host, run the following command:

      **helm package {name}/**

      In the preceding command, replace **{name}** with the actual chart name.

#. Set the chart directory structure and name the chart based on the requirements defined in :ref:`Chart Specifications <cce_01_0144__s8af9336c49a44399865c7a0f3149d789>`.

.. _cce_01_0144__s8af9336c49a44399865c7a0f3149d789:

Chart Specifications
--------------------

This section uses the redis chart as an example to illustrate the chart specifications.

-  **Naming Requirement**

   A chart package is named in the format of **{name}-{version}**.tgz, where **{version}** indicates the version number in the format of *Major version number*.\ *Minor version number*.\ *Revision number*, for example, **redis-0.4.2.tgz**.

   .. note::

      The chart name {name} can contain a maximum of 64 characters.

      The version number must comply with the `semantic versioning <https://semver.org/>`__ rules.

      -  The main and minor version numbers are mandatory, and the revision number is optional.
      -  The major and minor version numbers and revision number must be integers, greater than or equal to 0, and less than or equal to 99.

-  **Directory Structure**

   The directory structure of a chart is as follows:

   .. code-block::

      redis/
        templates/
        values.yaml
        README.md
        Chart.yaml
        .helmignore

   As listed in :ref:`Table 1 <cce_01_0144__tb7d789a3467e4fe9b4385a51f3460321>`, the parameters marked with \* are mandatory.

   .. _cce_01_0144__tb7d789a3467e4fe9b4385a51f3460321:

   .. table:: **Table 1** Parameters in the directory structure of a chart

      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                                                                                                                              |
      +===================================+==========================================================================================================================================================================================================================================================================================================================================================================================================================+
      | \* templates                      | Stores all templates.                                                                                                                                                                                                                                                                                                                                                                                                    |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* values.yaml                    | Describes configuration parameters required by templates.                                                                                                                                                                                                                                                                                                                                                                |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                          |
      |                                   | .. important::                                                                                                                                                                                                                                                                                                                                                                                                           |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                          |
      |                                   |    NOTICE:                                                                                                                                                                                                                                                                                                                                                                                                               |
      |                                   |    Make sure that the image address set in the **values.yaml** file is the same as the image address in the container image repository. Otherwise, an exception occurs when you create a workload, and the system displays a message indicating that the image fails to be pulled.                                                                                                                                       |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                          |
      |                                   |    To obtain the image address, perform the following operations: Log in to the CCE console. In the navigation pane, choose **Image Repository** to access the SWR console. Choose **My Images** > **Private Images** and click the name of the uploaded image. On the **Image Tags** tab page, obtain the image address from the pull command. You can click to copy the command in the **Image Pull Command** column.  |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | README.md                         | A markdown file, including:                                                                                                                                                                                                                                                                                                                                                                                              |
      |                                   |                                                                                                                                                                                                                                                                                                                                                                                                                          |
      |                                   | -  The workload or services provided by the chart.                                                                                                                                                                                                                                                                                                                                                                       |
      |                                   | -  Prerequisites for running the chart.                                                                                                                                                                                                                                                                                                                                                                                  |
      |                                   | -  Configurations in the **values.yaml** file.                                                                                                                                                                                                                                                                                                                                                                           |
      |                                   | -  Information about chart installation and configuration.                                                                                                                                                                                                                                                                                                                                                               |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | \* Chart.yaml                     | Basic information about the chart.                                                                                                                                                                                                                                                                                                                                                                                       |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | .helmignore                       | Files or data that does not need to read templates during workload installation.                                                                                                                                                                                                                                                                                                                                         |
      +-----------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
