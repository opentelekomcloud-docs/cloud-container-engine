:original_name: cce_10_0152.html

.. _cce_10_0152:

Creating a ConfigMap
====================

Scenario
--------

A ConfigMap is a type of resource that stores configuration information required by a workload. Its content is user-defined. After creating ConfigMaps, you can use them as files or environment variables in a containerized workload.

ConfigMaps allow you to decouple configuration files from container images to enhance the portability of workloads.

Benefits of ConfigMaps:

-  Manage configurations of different environments and services.
-  Deploy workloads in different environments. Multiple versions are supported for configuration files so that you can update and roll back workloads easily.
-  Quickly import configurations in the form of files to containers.

Procedure
---------

#. Log in to the CCE console and access the cluster console.

#. Choose **ConfigMaps and Secrets** in the navigation pane and click **Create ConfigMap** in the upper right corner.

#. Set parameters.

   .. _cce_10_0152__table16321825732:

   .. table:: **Table 1** Parameters for creating a ConfigMap

      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                               |
      +===================================+===========================================================================================================================+
      | Name                              | Name of a ConfigMap, which must be unique in a namespace.                                                                 |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
      | Namespace                         | Namespace to which the ConfigMap belongs. If you do not specify this parameter, the value **default** is used by default. |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
      | Description                       | Description of the ConfigMap.                                                                                             |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
      | Data                              | Data of a ConfigMap, in the key-value pair format.                                                                        |
      |                                   |                                                                                                                           |
      |                                   | Click |image1| to add data. The value can be in string, JSON, or YAML format.                                             |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------+
      | Label                             | Label of the ConfigMap. Enter a key-value pair and click **Add**.                                                         |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------+

#. After the configuration is complete, click **OK**.

   The new ConfigMap is displayed in the ConfigMap list.

ConfigMap Requirements
----------------------

A ConfigMap resource file must be in YAML format, and the file size cannot exceed 2 MB.

The file name is **configmap.yaml** and the following shows a configuration example.

.. code-block::

   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: test-configmap
   data:
     data-1: value-1
     data-2: value-2

Creating a ConfigMap Using kubectl
----------------------------------

#. Configure the **kubectl** command to connect an ECS to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`.

#. Create and edit the **cce-configmap.yaml** file.

   **vi cce-configmap.yaml**

   .. code-block::

      apiVersion: v1
      kind: ConfigMap
      metadata:
        name: cce-configmap
      data:
        SPECIAL_LEVEL: Hello
        SPECIAL_TYPE: CCE

#. Run the following commands to create a ConfigMap.

   **kubectl create -f cce-configmap.yaml**

   **kubectl get cm**

   .. code-block::

      NAME               DATA            AGE
      cce-configmap      3               3h
      cce-configmap1     3               7m

Related Operations
------------------

After creating a configuration item, you can update or delete it as described in :ref:`Table 2 <cce_10_0152__table1619535674020>`.

.. _cce_10_0152__table1619535674020:

.. table:: **Table 2** Related operations

   +-----------------------------------+------------------------------------------------------------------------------------------------------+
   | Operation                         | Description                                                                                          |
   +===================================+======================================================================================================+
   | Editing a YAML file               | Click **Edit YAML** in the row where the target ConfigMap resides to edit its YAML file.             |
   +-----------------------------------+------------------------------------------------------------------------------------------------------+
   | Updating a ConfigMap              | #. Select the name of the ConfigMap to be updated and click **Update**.                              |
   |                                   | #. Modify the secret data. For more information, see :ref:`Table 1 <cce_10_0152__table16321825732>`. |
   |                                   | #. Click **OK**.                                                                                     |
   +-----------------------------------+------------------------------------------------------------------------------------------------------+
   | Deleting a ConfigMap              | Select the configuration you want to delete and click **Delete**.                                    |
   |                                   |                                                                                                      |
   |                                   | Follow the prompts to delete the ConfigMap.                                                          |
   +-----------------------------------+------------------------------------------------------------------------------------------------------+

.. |image1| image:: /_static/images/en-us_image_0000001205757902.png
