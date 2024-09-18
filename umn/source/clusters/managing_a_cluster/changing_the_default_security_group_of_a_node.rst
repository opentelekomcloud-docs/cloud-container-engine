:original_name: cce_10_0426.html

.. _cce_10_0426:

Changing the Default Security Group of a Node
=============================================

Scenario
--------

When creating a cluster, you can customize a node security group to centrally manage network security policies. For a created cluster, you can change its default node security group.

Notes and Constraints
---------------------

-  Do not add more than 1000 pods to the same security group. Otherwise, the security group performance may be impacted.
-  Exercise caution when modifying the security group rules of a master node.

Procedure
---------

#. Log in to the CCE console. In the navigation pane, choose **Clusters**.
#. Click the cluster name to access the **Overview** page.
#. In the **Network Configuration** area, click |image1| next to the **Default Node Security Group**.
#. Select an existing security group, confirm that the security group rules meet the cluster requirements, and click **OK**.

   .. important::

      -  Ensure that correct port rules are configured for the selected security group. Otherwise, the node cannot be created. The port rules that a security group must comply with vary with the cluster type.
      -  The new security group takes effect only for newly created or managed nodes. For existing nodes, modify the security group rules and reset the nodes in real time. The original security group is still used.

.. |image1| image:: /_static/images/en-us_image_0000001981436137.png
