:original_name: cce_10_0152.html

.. _cce_10_0152:

Creating a ConfigMap
====================

Scenario
--------

A ConfigMap is a Kubernetes resource used to store non-sensitive configuration data. Its content is defined by users. After a ConfigMap is created, containerized workloads can use it through volume mounts or environment variable injection.

ConfigMaps decouple configuration files from container images, improving workload portability and preventing unnecessary image rebuilds caused by configuration changes.

The core benefits of ConfigMaps include:

-  Easy injection of configuration files into containers: Configuration files can be mounted directly to a specified path in a container, allowing configuration updates without modifying the image.
-  Unified management across multiple environments and services: Separate ConfigMaps can be created for different environments (such as development, testing, and production) or different service lines. This enables configuration isolation and efficient management.
-  Agile workload deployment and version control: The same workload can be deployed in different environments using different ConfigMaps. ConfigMaps support versioning, enabling efficient workload updates, iterations, and quick rollbacks.

Notes and Constraints
---------------------

-  The size of a ConfigMap resource file cannot exceed 1 MiB.
-  ConfigMaps cannot be used in `static pods <https://kubernetes.io/docs/tasks/configure-pod-container/static-pod/>`__.

Creating a ConfigMap Using the Console
--------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. Choose **ConfigMaps and Secrets** in the navigation pane and click **Create ConfigMap** in the upper right corner.

#. Configure parameters.

   .. _cce_10_0152__table16321825732:

   .. table:: **Table 1** Parameters for creating a ConfigMap

      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                           |
      +===================================+=======================================================================================================================================================================================================================================================+
      | Name                              | Name of the ConfigMap you create, which must be unique in a namespace.                                                                                                                                                                                |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Namespace                         | Namespace to which the ConfigMap belongs. If you do not specify this parameter, the value **default** is used by default.                                                                                                                             |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Description                       | Description of the ConfigMap.                                                                                                                                                                                                                         |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Data                              | Data of the ConfigMap. Click **Add** and enter key-value pairs.                                                                                                                                                                                       |
      |                                   |                                                                                                                                                                                                                                                       |
      |                                   | -  **Key**: Enter 1 to 63 characters. Only digits, letters, periods (.), hyphens (-), and underscores (_) are allowed. The key cannot start with two periods (..).                                                                                    |
      |                                   | -  **Value**: The value can be in string, JSON, or YAML format.                                                                                                                                                                                       |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Tag Management (Optional)         | Labels of the ConfigMap. Click **Add Label** and enter key-value pairs. The key and value must contain 1 to 63 characters that start and end with a letter or digit. Only letters, digits, hyphens (-), underscores (_), and periods (.) are allowed. |
      +-----------------------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **OK**.

   The new ConfigMap is displayed in the ConfigMap list.

Creating a ConfigMap Using kubectl
----------------------------------

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Create a file named **cce-configmap.yaml** and edit it.

   .. code-block::

      vi cce-configmap.yaml

   File content:

   .. code-block::

      apiVersion: v1
      kind: ConfigMap
      metadata:
        name: cce-configmap
      data:
        SPECIAL_LEVEL: Hello
        SPECIAL_TYPE: CCE

   .. table:: **Table 2** Key parameters

      ============= ==================================================
      Parameter     Description
      ============= ==================================================
      apiVersion    The value is fixed at **v1**.
      kind          The value is fixed at **ConfigMap**.
      metadata.name ConfigMap name, which can be customized.
      data          ConfigMap data. The value must be key-value pairs.
      ============= ==================================================

#. Run the following commands to create the ConfigMap:

   .. code-block::

      kubectl create -f cce-configmap.yaml

   Run the following commands to view the created ConfigMap:

   .. code-block::

      kubectl get cm

   Command output:

   .. code-block::

      NAME               DATA            AGE
      cce-configmap      2               7m

Related Operations
------------------

After creating a ConfigMap, you can update or delete it as described in :ref:`Table 3 <cce_10_0152__table1619535674020>`.

.. _cce_10_0152__table1619535674020:

.. table:: **Table 3** Related operations

   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Operation                         | Description                                                                                                                                                                                                    |
   +===================================+================================================================================================================================================================================================================+
   | Editing a ConfigMap's YAML file   | Click **Edit YAML** in the row where the target ConfigMap resides to edit its YAML file.                                                                                                                       |
   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Updating a ConfigMap              | #. Select the name of the ConfigMap to be updated and click **Update**.                                                                                                                                        |
   |                                   | #. Modify the information by referring to :ref:`Table 1 <cce_10_0152__table16321825732>`.                                                                                                                      |
   |                                   |                                                                                                                                                                                                                |
   |                                   |    .. note::                                                                                                                                                                                                   |
   |                                   |                                                                                                                                                                                                                |
   |                                   |       If you enable **Version Management** during the ConfigMap update, CCE automatically generates a historical version for the ConfigMap before modification for data management and rollback.               |
   |                                   |                                                                                                                                                                                                                |
   |                                   | #. Click **OK**.                                                                                                                                                                                               |
   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Deleting a ConfigMap              | Select the target ConfigMap and choose **More** > **Delete**.                                                                                                                                                  |
   |                                   |                                                                                                                                                                                                                |
   |                                   | Delete the ConfigMap as instructed.                                                                                                                                                                            |
   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Rolling back a ConfigMap          | Select the target ConfigMap and choose **More** > **Rollback** to roll back it to the specified historical version as instructed. This function is available only when the ConfigMap has a historical version. |
   +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
