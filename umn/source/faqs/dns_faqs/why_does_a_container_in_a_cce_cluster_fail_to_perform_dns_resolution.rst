:original_name: cce_faq_00107.html

.. _cce_faq_00107:

Why Does a Container in a CCE Cluster Fail to Perform DNS Resolution?
=====================================================================

Symptom
-------

A customer bound its domain name to the private domain names in the DNS service and also to a specific VPC. It is found that the ECSs in the VPC can properly resolve the private domain name but the containers in the VPC cannot.

Application Scenario
--------------------

Containers in a VPC cannot resolve domain names.

Solution
--------

According to the resolution rules of private domain names, the subnet DNS in the VPC must be set to the cloud DNS. You can find the details of the private network DNS service on its console.

The customer can perform domain name resolution on the ECSs in the VPC subnet, which indicates that the preceding configuration has been completed in the subnet.

|image1|

However, when the domain name resolution is performed in a container, the message "bad address" is displayed, indicating that the domain name cannot be resolved.

|image2|

Log in to the CCE console and check the add-ons installed in the cluster.

If you find that the coredns add-on does not exist in **Add-ons Installed**, the coredns add-on may have been incorrectly uninstalled.

Install it and add the corresponding domain name and DNS service address to resolve the domain name.

.. |image1| image:: /_static/images/en-us_image_0000001981275157.jpg
.. |image2| image:: /_static/images/en-us_image_0000001950315632.jpg
