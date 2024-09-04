:original_name: cce_faq_00195.html

.. _cce_faq_00195:

How Do I Optimize the Configuration If the External Domain Name Resolution Is Slow or Times Out?
================================================================================================

The following is an example **resolv.conf** file for a container in a workload:

|image1|

In the preceding information:

-  **nameserver**: IP address of the DNS. Set this parameter to the cluster IP address of CoreDNS.
-  **search**: domain name search list, which is a common suffix of Kubernetes.
-  **ndots**: If the number of dots (.) is less than the domain name, **search** is preferentially used for resolution.
-  **timeout**: timeout interval.
-  **single-request-reopen**: indicates that different source ports are used to send different types of requests.

By default, when you create a workload on the CCE console, the preceding parameters are configured as follows:

.. code-block::

         dnsConfig:
           options:
             - name: timeout
               value: '2'
             - name: ndots
               value: '5'
             - name: single-request-reopen

These parameters can be optimized or modified based on service requirements.

Scenario 1: Slow External Domain Name Resolution
------------------------------------------------

Optimization Solution

#. If the workload does not need to access the Kubernetes Service in the cluster, see :ref:`How Do I Configure a DNS Policy for a Container? <cce_faq_00194>`
#. If the number of dots (.) in the domain name used by the working Service to access other Kubernetes Services is less than 2, set **ndots** to **2**.

Scenario 2: External Domain Name Resolution Timeout
---------------------------------------------------

Optimization Solution

#. Generally, the timeout of a Service must be greater than the value of **timeout** multiplied by **attempts**.
#. If it takes more than 2s to resolve the domain name, you can set **timeout** to a larger value.

.. |image1| image:: /_static/images/en-us_image_0000001950315760.png
