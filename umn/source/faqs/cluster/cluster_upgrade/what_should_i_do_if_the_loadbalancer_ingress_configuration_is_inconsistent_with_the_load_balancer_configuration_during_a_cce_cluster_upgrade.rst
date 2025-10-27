:original_name: cce_faq_00493.html

.. _cce_faq_00493:

What Should I Do If the LoadBalancer Ingress Configuration Is Inconsistent with the Load Balancer Configuration During a CCE Cluster Upgrade?
=============================================================================================================================================

ELB Resources
-------------

In a CCE cluster, LoadBalancer ingresses are used to route external traffic to Services within the cluster. The parameters defined in an ingress are applied to configure a load balancer for effective traffic management and distribution. :ref:`Table 1 <cce_faq_00493__table3174193673018>` lists the mapping between load balancer configuration and ingress parameters.

.. _cce_faq_00493__table3174193673018:

.. table:: **Table 1** Mapping between load balancer configuration and LoadBalancer ingresses

   +----------------------------------+----------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Load Balancer Configuration      |                            | Description                                                                                                                                                                                                                                                                                                                                                                      | Mapping in a LoadBalancer Ingress                                                                                                                             |
   +==================================+============================+==================================================================================================================================================================================================================================================================================================================================================================================+===============================================================================================================================================================+
   | Load balancer (**elb**)          | ``-``                      | Distribute incoming traffic across backend servers in one or more AZs.                                                                                                                                                                                                                                                                                                           | **kubernetes.io/elb.id** in ingress annotations                                                                                                               |
   +----------------------------------+----------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Listener (**listener**)          | ``-``                      | Use the protocol and port you specify to check for requests from clients and route the requests to associated backend servers based on the routing rules you define.                                                                                                                                                                                                             | **kubernetes.io/elb.port** in ingress annotations, which defaults to **80/443** if not defined                                                                |
   +----------------------------------+----------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Forwarding policy (**l7policy**) | Forwarding policy          | **Load balancer forwarding rules**: domain names or paths                                                                                                                                                                                                                                                                                                                        | Domain name and path in the forwarding rule: **spec.rules[].host** and **spec.rules[].http.paths[].path** in the ingress parameters, respectively             |
   +----------------------------------+----------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                                  |                            | **Load balancer forwarding actions**: forward to a backend server group                                                                                                                                                                                                                                                                                                          | Backend server group: the backend service of the associated Service specified by **spec.rules[].http.paths[].backend.service** in the ingress                 |
   +----------------------------------+----------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                                  | Advanced forwarding policy | **Load balancer forwarding rules**: domain names, paths, HTTP request methods, HTTP headers, query strings, or CIDR blocks                                                                                                                                                                                                                                                       | The annotations related to advanced forwarding policies such as **kubernetes.io/elb.actions** and **kubernetes.io/elb.conditions** in the ingress annotations |
   +----------------------------------+----------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------+
   |                                  |                            | **Load balancer forwarding actions**: forward to a backend server group, redirect to another listener, redirect to another URL, return a specific response body, rewrite, write header, remove header, and limit request                                                                                                                                                         |                                                                                                                                                               |
   +----------------------------------+----------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Backend server group (**pool**)  | ``-``                      | A backend server group is a logical collection of one or more backend servers to receive massive requests concurrently. A backend server can be an ECS, supplementary network interface, or IP address.                                                                                                                                                                          | **spec.rules[].http.paths[].backend** in the ingress parameters                                                                                               |
   +----------------------------------+----------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Backend server (**member**)      | ``-``                      | Backend servers receive and process requests from the associated load balancer. For example, you can add an ECS as a backend server of a load balancer. The listener checks connection requests from clients using the configured protocol and port and forwards the requests to the backend servers in the backend server groups based on the load balancing algorithm you set. | Endpoint of the Service associated with the ingress                                                                                                           |
   +----------------------------------+----------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------+

Troubleshooting Inconsistency Between Ingress and Load Balancer Configuration
-----------------------------------------------------------------------------

During a pre-upgrade cluster check, many discrepancies between ingress and load balancer configuration may be identified. For each inconsistent item, CCE provides the expected configuration details. An example is as follows:

.. code-block::

   inconsistent: ingress(default/nginx) need create listener(HTTP/8000) of elb(4a090bf3-9d11-45dc-8d04-f9cf010ba2ec), the wanted configuration is {"name":"k8s_HTTP_8000","protocol_port":8000,"protocol":"HTTP","loadbalancer_id":"4a090bf3-9d11-45dc-8d04-f9cf010ba2ec","sni_container_refs":null,"http2_enable":false}

   inconsistent: ingress(default/nginx) need create pool(k8s_default_nginx-80_HTTP-8000), the wanted configuration is {"name":"k8s_default_nginx-80_HTTP-8000","protocol":"HTTP","loadbalancer_id":"4a090bf3-9d11-45dc-8d04-f9cf010ba2ec","lb_algorithm":"ROUND_ROBIN","slow_start":{"enable":false,"duration":30}}

   inconsistent: need create member(address: 172.16.0.54) of pool(k8s_default_nginx-80_HTTP-8000), the wanted configuration is {"name":"2c2405d1abc17da48c70e9edc9a340fc","subnet_cidr_id":"77b9ad29-e30f-451b-92e7-949b83220b0f","address":"172.16.0.54","protocol_port":30838,"weight":6}

   inconsistent: ingress(default/nginx) need create l7policy(k8s_default_nginx_6666cd76) of listener(k8s_HTTP_8000/HTTP/8000), the wanted configuration is {"name":"k8s_default_nginx_6666cd76","listener_id":"k8s_HTTP_8000/HTTP/8000","action":"REDIRECT_TO_POOL","redirect_pool_id":"k8s_default_nginx-80_HTTP-8000"}

    inconsistent: ingress(default/nginx) need create rule of l7policy(k8s_default_nginx_6666cd76), the wanted configuration is {"type":"PATH","compare_type":"STARTS_WITH","value":"/"}

You can perform the following operations in sequence.

+-----------------------+----------------------------------+-----------------------------------------------------------------------------------------------------------------------------+
| No.                   | Exception                        | Cause                                                                                                                       |
+=======================+==================================+=============================================================================================================================+
| 1                     | Listener (**listener**)          | -  :ref:`Inconsistent Listeners: A Listener Needs to Be Created <cce_faq_00493__section136992373337>`                       |
|                       |                                  | -  :ref:`Inconsistent Listeners: A Listener Needs to Be Updated <cce_faq_00493__section6284152193215>`                      |
+-----------------------+----------------------------------+-----------------------------------------------------------------------------------------------------------------------------+
| 2                     | Forwarding policy (**l7policy**) | -  :ref:`Inconsistent Forwarding Policies: A Forwarding Policy Needs to Be Created <cce_faq_00493__section673881153516>`    |
|                       |                                  | -  :ref:`Inconsistent Forwarding Policies: A Forwarding Policy Needs to Be Updated <cce_faq_00493__section1019018101411>`   |
|                       |                                  | -  :ref:`Inconsistent Forwarding Policies: A Forwarding Policy Needs to Be Deleted <cce_faq_00493__section387213223514>`    |
+-----------------------+----------------------------------+-----------------------------------------------------------------------------------------------------------------------------+
| 3                     | Forwarding rule (**rule**)       | :ref:`Inconsistent Forwarding Rules: A Forwarding Rule Needs to Be Created or Deleted <cce_faq_00493__section141433215711>` |
+-----------------------+----------------------------------+-----------------------------------------------------------------------------------------------------------------------------+
| 4                     | Backend server group (**pool**)  | :ref:`Inconsistent Backend Server Groups: A Backend Server Group Needs to Be Created <cce_faq_00493__section167634021114>`  |
+-----------------------+----------------------------------+-----------------------------------------------------------------------------------------------------------------------------+
| 5                     | Backend server (**member**)      | -  :ref:`Inconsistent Backend Servers: A Backend Server Needs to Be Created <cce_faq_00493__section245016225144>`           |
|                       |                                  | -  :ref:`Inconsistent Backend Servers: A Backend Server Needs to Be Updated <cce_faq_00493__section1059163341518>`          |
|                       |                                  | -  :ref:`Inconsistent Backend Servers: A Backend Server Needs to Be Deleted <cce_faq_00493__section96671714141615>`         |
+-----------------------+----------------------------------+-----------------------------------------------------------------------------------------------------------------------------+
| 6                     | Health check (**healthMonitor**) | -  :ref:`Inconsistent Health Check Settings: Health Checks Need to Be Removed <cce_faq_00493__section122033159171>`         |
|                       |                                  | -  :ref:`Inconsistent Health Check Settings: Health Checks Need to Be Updated <cce_faq_00493__section818413427444>`         |
+-----------------------+----------------------------------+-----------------------------------------------------------------------------------------------------------------------------+

.. _cce_faq_00493__section136992373337:

Inconsistent Listeners: A Listener Needs to Be Created
------------------------------------------------------

.. code-block::

   inconsistent: ingress(xxx) need create listener ...

When a listener's name or description is modified or a listener is deleted from the ELB console, this problem arises. In this case, you can use the port to check whether the listener is available on the ELB console and if its name and description match the expected configuration.

|image1|

The listener description should resemble the following, where **cluster_id** represents the ID of the cluster:

.. code-block::

   {"attention":"Auto-generated by CCE service, do not modify!","cluster_id":"5d4d44bd-0891-11f0-84d3-0255ac10003e"}

For example:

.. code-block::

   inconsistent: ingress(default/nginx) need create listener(HTTP/8000) of elb(4a090bf3-9d11-45dc-8d04-f9cf010ba2ec), the wanted configuration is {"name":"k8s_HTTP_8000","protocol_port":8000,"protocol":"HTTP","loadbalancer_id":"4a090bf3-9d11-45dc-8d04-f9cf010ba2ec","sni_container_refs":null,"http2_enable":false}

This indicates that an Nginx ingress in the **default** namespace needs a listener with port 8000 and protocol HTTP under the load balancer **4a090bf3-9d11-45dc-8d04-f9cf010ba2ec**. The listener should be named **k8s_HTTP_8000**, with **sni_container_refs** set to **null** and **http2_enable** set to **false**. **The description also needs to be specified.**

.. _cce_faq_00493__section6284152193215:

Inconsistent Listeners: A Listener Needs to Be Updated
------------------------------------------------------

.. code-block::

   inconsistent: ingress(xxx) need update listener ...

When the listener configuration is modified on the ELB console but is not synchronized with CCE, this problem arises. In this case, you can check the ELB console for the actual configuration and compare it with the expected configuration. If there is any inconsistency, you can update the listener manually or modify the ingress as needed.

For example:

.. code-block::

   inconsistent: ingress(default/cce-elb-ingress) need update listener(5ca8a931-9d7a-465e-b503-08f88d528cd8), the wanted configuration is {"id":"5ca8a931-9d7a-465e-b503-08f88d528cd8","sni_container_refs":null}

The expected configuration of the listener is displayed after **the wanted configuration is**. In this example, the expected configuration is **sni_container_refs: null**. In this case, you can check whether the SNI function is enabled for the ELB listener but is missing in the ingress. If so, add the SNI configuration to the ingress. For details, see :ref:`Configuring SNI for a LoadBalancer Ingress <cce_10_0688>`.

.. _cce_faq_00493__section673881153516:

Inconsistent Forwarding Policies: A Forwarding Policy Needs to Be Created
-------------------------------------------------------------------------

.. code-block::

   inconsistent: ingress(xxx) need create l7policy(xxx) ...

When the description of a forwarding policy created by CCE is modified on the ELB console or a forwarding policy created by CCE is deleted on the ELB console, this problem arises. In this case, you can verify the policy's existence on the ELB console by its name and check whether the description matches the expected configuration.

|image2|

The following shows an example for the description of a forwarding policy. The value of **cluster_id** is the ID of the current cluster, and the value of **ingress_id** is the UID of the current ingress.

.. code-block::

   {"attention":"Auto-generated by CCE service, do not modify!","cluster_id":"5d4d44bd-0891-11f0-84d3-0255ac10003e","ingress_id":"8a79a17b-f9ab-4431-95f6-0e1125093aac"}

**The ELB console does not provide a direct view of forwarding policy descriptions. To access this information, you must retrieve it from the data returned through the browser interface.**

For example:

.. code-block::

   inconsistent: ingress(default/nginx) need create l7policy(k8s_default_nginx_6666cd76) of listener(0bdabd92-a85b-4f9b-96df-0fde706ea028), the wanted configuration is {"name":"k8s_default_nginx_6666cd76","listener_id":"0bdabd92-a85b-4f9b-96df-0fde706ea028","action":"REDIRECT_TO_POOL","redirect_pool_id":"k8s_default_nginx-80_HTTP-8000"}

This indicates that the Nginx ingress in the **default** namespace needs a forwarding policy named **k8s_default_nginx_6666cd76** under the listener **0bdabd92-a85b-4f9b-96df-0fde706ea028**, and traffic needs to be forwarded to the backend server group named **k8s_default_nginx-80_HTTP-8000**. **The description also needs to be specified.** You can call the API for creating a forwarding policy.

You can view the created forwarding policy in the returned information via the browser interface.

.. _cce_faq_00493__section1019018101411:

Inconsistent Forwarding Policies: A Forwarding Policy Needs to Be Updated
-------------------------------------------------------------------------

.. code-block::

   inconsistent: ingress(xxx) need update l7policy(xxx) of listener(xxx) ...

When the backend server group (**redirect_pool_id**) of the forwarding policy created by CCE is modified on the ELB console, this problem arises. For example:

.. code-block::

   inconsistent: ingress(default/nginx) need update l7policy(c4beb313-0a32-4add-9d0e-68b8f0f90e01) of listener(0bdabd92-a85b-4f9b-96df-0fde706ea028), the wanted configuration is {"id":"c4beb313-0a32-4add-9d0e-68b8f0f90e01","redirect_pool_id":"k8s_default_nginx-80_HTTP-8000","redirect_pools_config":[]}

This indicates that for the Nginx ingress in the **default** namespace, the **redirect_pool_id** in the forwarding policy **c4beb313-0a32-4add-9d0e-68b8f0f90e01** must be updated to the backend server group ID of **k8s_default_nginx-80_HTTP-8000** under the listener **0bdabd92-a85b-4f9b-96df-0fde706ea028**. Additionally, the **redirect_pools_config** should be deleted.

.. _cce_faq_00493__section387213223514:

Inconsistent Forwarding Policies: A Forwarding Policy Needs to Be Deleted
-------------------------------------------------------------------------

.. code-block::

   inconsistent: ingress(xxx) need delete l7policy(id: xxx) ...

When a forwarding policy is created on the ELB console for the listener that is created by CCE, this problem arises. In this case, you can locate the forwarding policy based on its ID and then delete it.

For example:

.. code-block::

   inconsistent: need delete l7policy(id: f4eda0af-869a-47fb-a699-2f27f286b641)

You can call the API for viewing details of a forwarding policy to see which ELB listener the forwarding policy applies and then delete the forwarding policy. If you want to keep the forwarding policy, add the corresponding configuration to the ingress.

.. _cce_faq_00493__section141433215711:

Inconsistent Forwarding Rules: A Forwarding Rule Needs to Be Created or Deleted
-------------------------------------------------------------------------------

.. code-block::

   inconsistent: ingress(xxx) need create rule of l7policy(xxx) ...

When the forwarding rule of a forwarding policy created by CCE is modified on the ELB console, this problem arises. Typically, changes to the rule type, matching mode, or path result in discrepancies. CCE handles forwarding rule updates by deleting and recreating them, which often leads to simultaneous creation and deletion issues.

For example:

.. code-block::

   inconsistent: ingress(default/nginx) need create rule of l7policy(k8s_default_nginx_6666cd76), the wanted configuration is {"type":"PATH","compare_type":"STARTS_WITH","value":"/"}

   inconsistent: need delete rule(6cf87a3e-373e-4378-bf7a-16a709feee63) of l7policy(c4beb313-0a32-4add-9d0e-68b8f0f90e01)

This indicates that for the Nginx ingress in the **default** namespace, a forwarding rule needs to be created for the **k8s_default_nginx_6666cd76** policy. The rule's type should be **PATH**, the matching mode should be **STARTS_WITH** (prefix matching), and the value should be **/**. Additionally, you need to delete the forwarding rule **6cf87a3e-373e-4378-bf7a-16a709feee63** from the forwarding policy **c4beb313-0a32-4add-9d0e-68b8f0f90e01**.

.. _cce_faq_00493__section167634021114:

Inconsistent Backend Server Groups: A Backend Server Group Needs to Be Created
------------------------------------------------------------------------------

.. code-block::

   inconsistent: need create pool(xxx)...

When the backend server group (**redirect_pool_id**) of the forwarding policy created by CCE is modified on the ELB console, this problem arises. Typically, the system displays the message "need update l7policy ...". In this case, you can check if the backend server group exists by its name or listener. If the group exists, update the backend server group of the forwarding policy.

For example:

.. code-block::

   inconsistent: ingress(default/nginx) need create pool(k8s_default_nginx-80_HTTP-8000), the wanted configuration is {"name":"k8s_default_nginx-80_HTTP-8000","protocol":"HTTP","loadbalancer_id":"4a090bf3-9d11-45dc-8d04-f9cf010ba2ec","lb_algorithm":"ROUND_ROBIN","slow_start":{"enable":false,"duration":30}}

If the group does not exist, create one based on the printed configuration items.

.. _cce_faq_00493__section245016225144:

Inconsistent Backend Servers: A Backend Server Needs to Be Created
------------------------------------------------------------------

.. code-block::

   inconsistent: need create member(address: xxx) of pool(xxx) ...

When a backend server in a backend server group created by CCE is modified on the ELB console, this problem arises.

For example:

.. code-block::

   inconsistent: need create member(address: 172.16.0.54) of pool(dab7b0c1-623e-4499-8c70-7477f36fc2ef), the wanted configuration is {"name":"2c2405d1abc17da48c70e9edc9a340fc","subnet_cidr_id":"77b9ad29-e30f-451b-92e7-949b83220b0f","address":"172.16.0.54","protocol_port":30838,"weight":6}

This indicates that a backend server **2c2405d1abc17da48c70e9edc9a340fc** with the IP address **172.16.0.54**, port 30838, weight 6, and subnet **77b9ad29-e30f-451b-92e7-949b83220b0f** needs to be added to the backend server group **dab7b0c1-623e-4499-8c70-7477f36fc2ef**. You cannot enter a backend server name on the ELB console, but you can call the API for adding a backend server.

.. _cce_faq_00493__section1059163341518:

Inconsistent Backend Servers: A Backend Server Needs to Be Updated
------------------------------------------------------------------

.. code-block::

   inconsistent: need update member(address: xxx) of pool(xxx) ...

When the weight of a backend server in the backend server group created by CCE is changed on the ELB console, this problem arises.

For example:

.. code-block::

   inconsistent: need update member(9a65b96c-891a-439e-a692-508c3e095095) of pool(dab7b0c1-623e-4499-8c70-7477f36fc2ef), the wanted configuration is {"id":"9a65b96c-891a-439e-a692-508c3e095095","name":"2c2405d1abc17da48c70e9edc9a340fc","weight":6}

This indicates that you need to change the weight of the backend server **9a65b96c-891a-439e-a692-508c3e095095** in the backend server group **dab7b0c1-623e-4499-8c70-7477f36fc2ef** to **6**.

.. _cce_faq_00493__section96671714141615:

Inconsistent Backend Servers: A Backend Server Needs to Be Deleted
------------------------------------------------------------------

.. code-block::

   inconsistent: need delete member(address: xxx) of pool(xxx) ...

When a backend server in a backend server group created by CCE is modified on the ELB console, this problem arises.

For example:

.. code-block::

   inconsistent: need delete members([d0891296-daf9-496b-9cac-ad1a1101b303]) of pool(dab7b0c1-623e-4499-8c70-7477f36fc2ef)

This indicates that you need to delete the backend server **d0891296-daf9-496b-9cac-ad1a1101b303** from the backend server group **dab7b0c1-623e-4499-8c70-7477f36fc2ef**.

.. _cce_faq_00493__section122033159171:

Inconsistent Health Check Settings: Health Checks Need to Be Removed
--------------------------------------------------------------------

.. code-block::

   inconsistent: need delete healthMonitor(xxx) of pool(xxx) ...

When the health checks of a backend server group created by CCE are enabled on the ELB console but are not configured in the ingress, this problem arises.

For example:

.. code-block::

   inconsistent: need delete healthMonitor(ef7ad2c3-bdda-4727-8a1c-de37eeb48b55) of pool(402dbff8-58b8-4a49-bb99-0dc5cf87c35b)

This indicates that the health check associated with the backend server group **402dbff8-58b8-4a49-bb99-0dc5cf87c35b** needs to be removed. You can call the API for deleting a health check. You can also enable the health check in the ingress.

.. _cce_faq_00493__section818413427444:

Inconsistent Health Check Settings: Health Checks Need to Be Updated
--------------------------------------------------------------------

.. code-block::

   inconsistent: need update healthMonitor(xxx) of pool(xxx) ...

When the health checks of a backend server group created by CCE are modified on the ELB console, this problem arises.

For example:

.. code-block::

   inconsistent: need update healthMonitor(9d438b19-4342-42af-a876-77d49bbc9447) of pool(402dbff8-58b8-4a49-bb99-0dc5cf87c35b), the wanted configuration is {"delay":5,"max_retries":3,"timeout":10,"type":"HTTP","url_path":"/","monitor_port":null,"admin_state_up":true}

This indicates that the health check **9d438b19-4342-42af-a876-77d49bbc9447** must be updated to match the desired settings.

Common Issues
-------------

-  Why is "need create" displayed when the listener, Layer 7 policy (**l7policy**), or pool exists?

   **Solution**

   Resources managed by CCE must have descriptions. You need to check whether the descriptions exist.

.. |image1| image:: /_static/images/en-us_image_0000002434238176.png
.. |image2| image:: /_static/images/en-us_image_0000002467716769.png
