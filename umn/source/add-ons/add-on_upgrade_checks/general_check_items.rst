:original_name: cce_10_1031.html

.. _cce_10_1031:

General Check Items
===================

Check Items
-----------

-  :ref:`Add-on Status <cce_10_1031__section16401621161915>`
-  :ref:`Add-on Settings <cce_10_1031__section8719325184717>`
-  :ref:`Helm Charts <cce_10_1031__section1330111488475>`

.. _cce_10_1031__section16401621161915:

Add-on Status
-------------

**Description**

Not all pods or Services associated with the add-on are ready. So the add-on is not in the running state.

**Check Scope**

-  Supported checks

   -  Checks during add-on running
   -  Checks before add-on editing or upgrades

-  Supported clusters: clusters of v1.15 and later
-  Supported add-ons: all

**Risks**

-  If you continue to upgrade the cluster when the add-on is not ready, the add-on function may be abnormal, service traffic may be interrupted, and even the upgrade may be affected.
-  This risk is especially critical for core traffic-related add-ons (such as add-ons for ingresses and service meshes). Upgrading these key add-ons prematurely can significantly broaden the scope of impact.

**Solution**

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. In the navigation pane, choose **Add-ons**. Locate the add-on on the right and check its status.

   If the add-on is not running, further troubleshooting is required.

#. Check the status of the add-on pod. The CoreDNS add-on is used as an example.

   a. In the navigation pane, choose **Workloads**, click the **Pods** tab, and select the namespace to which the add-on belongs.

   b. Use the **release={releaseName}** label to locate the row containing the CoreDNS pod and choose **More** > **View YAML**.

      View the **status** field and:

      -  Check whether the value of **phase** is **Running**.
      -  Find the **Ready** type in the **conditions** field and check whether the value of **status** is **True**.

      -  Check whether the value of **ready** is **true** for all containers in the **containerStatuses** field.

      |image1|

   c. Click **View Events** to view the logs of the pod that is not ready and locate the cause of the startup failure.

#. Check the status of the Service associated with the add-on. If some add-ons are not associated with any Services, skip this step. NGINX Ingress Controller is used as an example.

   a. In the navigation pane, choose **Services & Ingresses** and select the namespace to which the Service belongs.

   b. Use **release={releaseName}** to search for the Service associated with the add-on, locate the row containing the **LoadBalancer** Service, and choose **More** > **View YAML**.

      The value of **status.loadBalancer.ingress** is not empty.

      |image2|

   c. Click **View Events** to view the events of the LoadBalancer Service and locate the cause of the load balancer failure.

#. After confirming that the pod and Service associated with the add-on are ready, check the add-on again and then edit or upgrade it.

.. _cce_10_1031__section8719325184717:

Add-on Settings
---------------

**Description**

During the add-on check, CCE runs the same configuration parameter verification logic used during add-on editing or upgrade requests. If the check result indicates that the add-on configuration is incorrect, it may be due to reasons like incorrect parameter settings, formatting that does not meet requirements, or missing mandatory parameters.

**Check Scope**

-  Supported checks: checks before add-on editing or upgrades
-  Supported clusters: clusters of v1.15 and later versions
-  Supported add-ons: all

**Risks**

Incorrect configuration settings will result in add-on editing or upgrade failures.

**Solution**

#. Locate the fault based on the **error message** in the check result.
#. Modify the parameter values based on the add-on parameter details or template requirements to ensure that the format and content comply with the verification rules.
#. After confirming that the error is rectified, check the add-on again and then edit or upgrade the add-on.

.. _cce_10_1031__section1330111488475:

Helm Charts
-----------

**Description**

The Kubernetes resources associated with the current add-on are incompatible with the expected Helm charts. When resource configurations deviate from the Helm-defined structure, particularly those that cannot be preserved after an upgrade, a chart check error is triggered. This issue is typically due to modifications made directly to the resources (for example, using kubectl).

**Check Scope**

-  Supported checks

   -  Checks during add-on running
   -  Checks before add-on editing or upgrades

-  Supported clusters: clusters of v1.19 and later versions
-  Supported resources and add-ons

   -  The cceaddon-nginx-ingress-controller Deployment and ConfigMaps of NGINX Ingress Controller
   -  ConfigMaps of CoreDNS

**Risks**

During an add-on upgrade, any changes that are incompatible with the expected Helm charts will be overwritten or discarded.

If these discrepancies are not detected in advance, they may lead to unintended consequences, such as rollback of service configurations, traffic interruption, or unintended changes in pod counts.

**Solution**

#. Check for differences between the existing Kubernetes resources and the expected chart-defined resources.

#. Modify the Kubernetes resources to align with the expected chart content.

#. If the resources cannot be fully restored to the expected state, but the differences can be addressed through an add-on configuration update, ignore the error and proceed with configuration.

   (Assume the NGINX Ingress Controller add-on has four pods, but the Helm chart specifies only two. In this case, you can update the add-on settings and set the pod count to **4**, ignore the difference, and deliver the configuration to ensure that the generated resources are the same as expected.)

.. |image1| image:: /_static/images/en-us_image_0000002484119160.png
.. |image2| image:: /_static/images/en-us_image_0000002516199131.png
