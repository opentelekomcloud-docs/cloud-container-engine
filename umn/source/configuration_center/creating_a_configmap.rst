:original_name: cce_01_0152.html

.. _cce_01_0152:

Creating a ConfigMap
====================

Scenario
--------

A ConfigMap is a type of resource that stores configuration information required by a workload. Its content is user-defined. After creating ConfigMaps, you can use them as files or environment variables in a containerized workload.

ConfigMaps allow you to decouple configuration files from container images to enhance the portability of containerized workloads.

Benefits of ConfigMaps:

-  Manage configurations of different environments and services.
-  Deploy workloads in different environments. Multiple versions are supported for configuration files so that you can update and roll back workloads easily.
-  Quickly import configurations in the form of files to containers.

Prerequisites
-------------

Cluster and node resources have been created. For more information, see :ref:`Creating a CCE Cluster <cce_01_0028>`. If you have available clusters and node resources, skip this operation.

Procedure
---------

#. Log in to the CCE console. In the navigation pane, choose **Configuration Center** > **ConfigMaps**. Click **Create ConfigMap**.

#. You can create a ConfigMap directly or based on YAML. If you create a ConfigMap based on YAML, go to :ref:`4 <cce_01_0152__li2731182712159>`.

#. Method 1: Create a ConfigMap directly.

   Set the parameters by referring to :ref:`Table 1 <cce_01_0152__table16321825732>`.

   .. _cce_01_0152__table16321825732:

   .. table:: **Table 1** Parameters for creating a ConfigMap

      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                     |
      +===================================+=================================================================================================================================================================================+
      | Name                              | Name of a ConfigMap, which must be unique in a namespace.                                                                                                                       |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Cluster                           | Cluster that will use the ConfigMap you create.                                                                                                                                 |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Namespace                         | Namespace to which the ConfigMap belongs. If you do not specify this parameter, the value **default** is used by default.                                                       |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Description                       | Description of the ConfigMap.                                                                                                                                                   |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Data                              | The workload configuration data can be used in a container or used to store the configuration data. **Key** indicates a file name. **Value** indicates the content in the file. |
      |                                   |                                                                                                                                                                                 |
      |                                   | a. Click **Add Data**.                                                                                                                                                          |
      |                                   | b. Set **Key** and **Value**.                                                                                                                                                   |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Labels                            | Labels are attached to objects such as workloads, nodes, and Services in key-value pairs.                                                                                       |
      |                                   |                                                                                                                                                                                 |
      |                                   | Labels define the identifiable attributes of these objects and are used to manage and select the objects.                                                                       |
      |                                   |                                                                                                                                                                                 |
      |                                   | a. Click **Add Label**.                                                                                                                                                         |
      |                                   | b. Set **Key** and **Value**.                                                                                                                                                   |
      +-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. .. _cce_01_0152__li2731182712159:

   Method 2: Create a ConfigMap based on YAML.

   .. note::

      To create ConfigMaps by uploading a file, ensure that the resource description file has been created. CCE supports files in YAML format. For more information, see :ref:`ConfigMap Requirements <cce_01_0152__section66903416102>`.

   Click **Create YAML** on the right of the page.

   -  Method 1: Import the orchestration file.

      Click **Add File** to import the file in YAML format. The orchestration content can be directly displayed.

   -  Method 2: Directly orchestrate the content.

      In the orchestration content area, enter the content of the YAML file.

#. After the configuration is complete, click **Create**.

   The new ConfigMap is displayed in the ConfigMap list.

.. _cce_01_0152__section66903416102:

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

.. _cce_01_0152__section639712716372:

Creating a ConfigMap Using kubectl
----------------------------------

#. Configure the **kubectl** command to connect an ECS to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_01_0107>`.

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

After creating a configuration item, you can update or delete it as described in :ref:`Table 2 <cce_01_0152__table1619535674020>`.

.. _cce_01_0152__table1619535674020:

.. table:: **Table 2** Related operations

   +-----------------------------------+--------------------------------------------------------------------------------------------------------------+
   | Operation                         | Description                                                                                                  |
   +===================================+==============================================================================================================+
   | Viewing a YAML file               | Click **View YAML** next to the ConfigMap name to view the YAML file corresponding to the current ConfigMap. |
   +-----------------------------------+--------------------------------------------------------------------------------------------------------------+
   | Updating a ConfigMap              | #. Select the name of the ConfigMap to be updated and click **Update**.                                      |
   |                                   | #. Modify the secret data. For more information, see :ref:`Table 1 <cce_01_0152__table16321825732>`.         |
   |                                   | #. Click **Update**.                                                                                         |
   +-----------------------------------+--------------------------------------------------------------------------------------------------------------+
   | Deleting a ConfigMap              | Select the configuration you want to delete and click **Delete**.                                            |
   |                                   |                                                                                                              |
   |                                   | Follow the prompts to delete the ConfigMap.                                                                  |
   +-----------------------------------+--------------------------------------------------------------------------------------------------------------+
