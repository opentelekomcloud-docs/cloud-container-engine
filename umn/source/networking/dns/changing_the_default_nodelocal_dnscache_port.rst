:original_name: cce_10_0959.html

.. _cce_10_0959:

Changing the Default NodeLocal DNSCache Port
============================================

NodeLocal DNSCache defaults to using port 8080 on the host node, but this can cause conflicts with other services sharing the same port. This section describes how to change the default NodeLocal DNSCache port.

.. caution::

   You can change the default port of the NodeLocal DNSCache add-on of 1.6.66 or later.

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Choose **Add-ons** in the navigation pane, find the **NodeLocal DNSCache** add-on on the right, and click **Install**. If this add-on has been installed in the cluster, click **Edit**.
#. On the displayed page, configure parameters and click **Installation Using YAML** in the upper part of the page. If this add-on has been installed in the cluster, click **Edit YAML**.

   .. note::

      To simplify configuration, configure other parameters on the console, and then change the port number in the YAML file. For more information about other parameters, see :ref:`NodeLocal DNSCache <cce_10_0404>`.

#. Configure both parameters to the specified port number, ensuring they have the same value.

   a. Search for the **healthPort** parameter and change its value to a specified port number, for example, 8081.

      |image1|

   b. Search for the **cluster.local** parameter and change the value of **port** in the array to the specified port number, for example, 8081.

      |image2|

#. Click **OK**.

.. |image1| image:: /_static/images/en-us_image_0000002516079513.png
.. |image2| image:: /_static/images/en-us_image_0000002483959570.png
