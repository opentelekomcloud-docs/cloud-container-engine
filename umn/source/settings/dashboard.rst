:original_name: cce_10_0782.html

.. _cce_10_0782:

Dashboard
=========

Settings offers you an entry to check and modify the basic settings of clusters. It includes information from dimension like :ref:`Cluster Information <cce_10_0782__section1970341611713>`, :ref:`Cluster Settings <cce_10_0782__section138274223718>`, :ref:`Master Node AZs <cce_10_0782__section8442632978>`, and :ref:`Installed Add-ons <cce_10_0782__section15691481479>`.

Accessing the Settings
----------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Settings** and click the **Dashboard** tab.

.. _cce_10_0782__section1970341611713:

Cluster Information
-------------------

It includes:

-  **ID**: uniquely identifies a cluster resource. It is automatically generated after a cluster is created and can be used in scenarios such as API calling.
-  **Current Name**: After a cluster is created, you can click |image1| to change its name.
-  **Original Name**: specifies a cluster's original name after its current name is changed. The current name of a cluster must be unique.
-  **Status**: specifies the status of a cluster. For details, see :ref:`Cluster Lifecycle <cce_10_0430__section2048514305592>`.
-  **Type**: specifies whether a cluster is a CCE standard or a CCE Turbo cluster. For details about the differences between CCE standard and CCE Turbo clusters, see :ref:`Comparison Between Cluster Types <cce_10_0342>`.
-  **Created On**: specifies the time when a cluster was created. You will be billed based on the creation time of clusters.

.. _cce_10_0782__section138274223718:

Cluster Settings
----------------

After a cluster is created, you can modify the following items for it:

-  **Cluster Scale**: specifies how many nodes a cluster can manage at most. You can change the cluster scale as needed. For details, see :ref:`Changing Cluster Scale <cce_10_0403>`.
-  **Cluster Version \| Patch Version**: specifies the Kubernetes version and CCE patch version of a cluster.
-  **Network Model**: specifies the network model of a cluster, which cannot be changed. For details about network models, see :ref:`Container Network <cce_10_0280>`.
-  **Resource Tag**: You can add resource tags to classify resources.
-  **Cluster Description**: specifies the description that you entered for a cluster. A maximum of 200 characters are allowed.
-  **Cluster Deletion Protection**: A measure taken to prevent accidental deletion of clusters through the console or APIs. After this function is enabled, you will not be able to delete or unsubscribe from clusters on CCE.

.. _cce_10_0782__section8442632978:

Master Node AZs
---------------

You can check how many master nodes are supported in a cluster.

.. _cce_10_0782__section15691481479:

Installed Add-ons
-----------------

You can check the installed add-ons in a cluster. If there is an add-on to be upgraded in the cluster, click **Go to Upgrade** to go to the **Add-ons** page and see more details.

.. |image1| image:: /_static/images/en-us_image_0000002065480398.png
