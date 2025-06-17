:original_name: cce_10_0950.html

.. _cce_10_0950:

Forwarding Policy Priorities of LoadBalancer Ingresses
======================================================

CCE sets up forwarding policies on the ELB console based on the rules specified in the ingress configurations when creating LoadBalancer ingresses.

To handle more complex traffic routing needs, CCE has integrated the advanced forwarding policies of ELB, which means it supports advanced features like URL redirection and rewriting. However, note that the sorting logic for the advanced forwarding policies is different from the logic used for common forwarding policies.

Specifically:

-  If the ELB advanced forwarding policies are not enabled, the forwarding policies are sorted by domain name or path. For details, see :ref:`Default Sorting <cce_10_0950__section19493727181419>`.
-  After the ELB advanced forwarding policies are enabled, requests are matched based on the priorities and then forwarded to clients. For details, see :ref:`Priority <cce_10_0950__section106271844151416>`.

.. _cce_10_0950__section19493727181419:

Default Sorting
---------------

If the ELB advanced forwarding policies are not enabled, the default sorting rule of forwarding policies is as follows:

-  Forwarding rule priorities are independent of each other regardless of domain names. When a request matches both a domain name-based rule and a URL-based rule, the domain name-based rule is matched first.
-  URL-based forwarding rules are applied in the following order of priority: an exact match rule, a prefix match rule, and a regular expression match rule. For multiple matches of the same type, only the longest URL-based forwarding rule will be applied.

.. table:: **Table 1** Example of a forwarding policy with the default sorting

   ================== ================== =====
   Forwarding Policy  Specified Value    Order
   ================== ================== =====
   URL (exact match)  /test1/test2/test3 1
   URL (prefix match) /test1/test2       2
   URL (prefix match) /test1             3
   ================== ================== =====

Once a forwarding policy is created, the system automatically sorts it based on the default sorting rule. Examples are as follows:

-  The access request to **www.example.com/test1/test2** matches both forwarding policies 2 and 3. If the match types are the same, the priority is determined by the length of the URL, with longer URLs having higher priority. In this case, the access request will be forwarded based on the forwarding policy 2.
-  The access request to **www.example.com/test1/test2/test3** matches forwarding policies 1, 2, and 3. An exact match is preferred, so the access request will be forwarded based on the forwarding policy 1.

.. _cce_10_0950__section106271844151416:

Priority
--------

With ELB advanced forwarding policies enabled, each request is matched based on the forwarding policy priority (a smaller value indicates a higher priority). Once a forwarding policy is matched, the request is forwarded based on this forwarding policy.

The rules for configuring the priority of a forwarding policy are as follows:

-  The existing forwarding policies will maintain their original priority sequence before an advanced forwarding policy is configured.
-  Once an advanced forwarding policy is set up, any new forwarding policy added will have the lowest priority. The default forwarding policy always has the lowest priority and is not included in the sorting process. Additionally, you have the option to manually specify the priority of a new forwarding policy. For details, see :ref:`Configuring the Priorities of Forwarding Rules for LoadBalancer Ingresses <cce_10_0939>`.

.. table:: **Table 2** Example of a forwarding policy configured with priorities

   ================== =============== ========
   Forwarding Policy  Specified Value Priority
   ================== =============== ========
   URL (prefix match) /test1          1
   URL (exact match)  /test1          2
   ================== =============== ========

In this example, the access request to **www.example.com/test1** satisfies both forwarding policies 1 and 2, so the request will be forwarded based on the **forwarding policy 1**.
