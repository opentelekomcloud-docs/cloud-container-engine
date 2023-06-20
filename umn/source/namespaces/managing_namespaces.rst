:original_name: cce_10_0285.html

.. _cce_10_0285:

Managing Namespaces
===================

Using Namespaces
----------------

-  When creating a workload, you can select a namespace to isolate resources or users.
-  When querying workloads, you can select a namespace to view all workloads in the namespace.

Isolating Namespaces
--------------------

-  **Isolating namespaces by environment**

   An application generally goes through the development, joint debugging, and testing stages before it is launched. In this process, the workloads deployed in each environment (stage) are the same, but are logically defined. There are two ways to define them:

   -  Group them in different clusters for different environments.

      Resources cannot be shared among different clusters. In addition, services in different environments can access each other only through load balancing.

   -  Group them in different namespaces for different environments.

      Workloads in the same namespace can be mutually accessed by using the Service name. Cross-namespace access can be implemented by using the Service name or namespace name.

      The following figure shows namespaces created for the development, joint debugging, and testing environments, respectively.


      .. figure:: /_static/images/en-us_image_0000001569182513.png
         :alt: **Figure 1** One namespace for one environment

         **Figure 1** One namespace for one environment

-  **Isolating namespaces by application**

   You are advised to use this method if a large number of workloads are deployed in the same environment. For example, in the following figure, different namespaces (APP1 and APP2) are created to logically manage workloads as different groups. Workloads in the same namespace access each other using the Service name, and workloads in different namespaces access each other using the Service name or namespace name.


   .. figure:: /_static/images/en-us_image_0000001569022797.png
      :alt: **Figure 2** Grouping workloads into different namespaces

      **Figure 2** Grouping workloads into different namespaces

Deleting a Namespace
--------------------

If a namespace is deleted, all resources (such as workloads, jobs, and ConfigMaps) in this namespace will also be deleted. Exercise caution when deleting a namespace.

#. Log in to the CCE console and access the cluster console.

#. In the navigation pane, choose **Namespaces**, select the target namespace, and choose **More** > **Delete**.

   Follow the prompts to delete the namespace. The default namespaces cannot be deleted.
