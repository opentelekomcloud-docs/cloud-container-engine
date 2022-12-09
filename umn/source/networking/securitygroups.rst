:original_name: cce_01_0288.html

.. _cce_01_0288:

SecurityGroups
==============

When the Cloud Native Network 2.0 model is used, pods use VPC ENIs or sub-ENIs for networking. You can directly bind security groups and EIPs to pods. CCE provides a custom resource object named **SecurityGroup** for you to associate security groups with pods in CCE. You can customize workloads with specific security isolation requirements using SecurityGroups.

Notes and Constraints
---------------------

-  This function is supported for CCE Turbo clusters of v1.19 and later. Upgrade your CCE Turbo clusters if their versions are earlier than v1.19.
-  A workload can be bound to a maximum of five security groups.

Using the Console
-----------------

#. In the navigation pane of the CCE console, choose **Resource Management** > **Network**.

#. On the **SecurityGroup** tab page, select the target cluster in the upper right corner and click **Create**.

#. Set the parameters as described in :ref:`Table 1 <cce_01_0288__table572616321913>`.

   .. _cce_01_0288__table572616321913:

   .. table:: **Table 1** Configuration parameters

      +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------+
      | Parameter             | Description                                                                                                                                                                                                                                               | Example Value                        |
      +=======================+===========================================================================================================================================================================================================================================================+======================================+
      | SecurityGroup Name    | Enter a SecurityGroup name.                                                                                                                                                                                                                               | security-group                       |
      |                       |                                                                                                                                                                                                                                                           |                                      |
      |                       | Enter 4 to 63 characters. The value must start with a lowercase letter and cannot end with a hyphen (-). Only lowercase letters, digits, and hyphens (-) are allowed.                                                                                     |                                      |
      +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------+
      | Cluster Name          | Select a cluster.                                                                                                                                                                                                                                         | cce-turbo                            |
      +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------+
      | Namespace             | Select a namespace. If the namespace is not created, click **Create Namespace**.                                                                                                                                                                          | default                              |
      +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------+
      | Workload              | Select a workload.                                                                                                                                                                                                                                        | nginx                                |
      +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------+
      | Security Group        | The selected security group will be bound to the ENI or supplementary ENI of the selected workload. A maximum of five security groups can be selected from the drop-down list. You must select one or multiple security groups to create a SecurityGroup. | 64566556-bd6f-48fb-b2c6-df8f44617953 |
      |                       |                                                                                                                                                                                                                                                           |                                      |
      |                       | If no security group has not been created, click **Create Security Group**. After the security group is created, click the refresh button.                                                                                                                | 5451f1b0-bd6f-48fb-b2c6-df8f44617953 |
      |                       |                                                                                                                                                                                                                                                           |                                      |
      |                       | .. important::                                                                                                                                                                                                                                            |                                      |
      |                       |                                                                                                                                                                                                                                                           |                                      |
      |                       |    NOTICE:                                                                                                                                                                                                                                                |                                      |
      |                       |                                                                                                                                                                                                                                                           |                                      |
      |                       |    -  A maximum of 5 security groups can be selected.                                                                                                                                                                                                     |                                      |
      |                       |    -  Hover the cursor on the security group name, and you can view details about the security group.                                                                                                                                                     |                                      |
      +-----------------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+--------------------------------------+

#. After setting the parameters, click **Create**.

   After the SecurityGroup is created, the system automatically returns to the SecurityGroup list page. You can see that the newly added SecurityGroup is in the list.

Using kubectl
-------------

#. Use kubectl to connect to the cluster. For details, see :ref:`Connecting to a Cluster Using kubectl <cce_01_0107>`.

#. Create a description file named **securitygroup-demo.yaml**.

   **vi securitygroup-demo.yaml**

   For example, create the following SecurityGroup to bind all nginx workloads with two security groups 64566556-bd6f-48fb-b2c6-df8f44617953 and 5451f1b0-bd6f-48fb-b2c6-df8f44617953 that have been created in advance. An example is as follows:

   .. code-block::

      apiVersion: crd.yangtse.cni/v1
      kind: SecurityGroup
      metadata:
        name: demo
        namespace: default
      spec:
        podSelector:
          matchLabels:
            app: nginx
        securityGroups:
        - id: 64566556-bd6f-48fb-b2c6-df8f44617953
        - id: 5451f1b0-bd6f-48fb-b2c6-df8f44617953

   :ref:`Table 2 <cce_01_0288__table132326831016>` describes the parameters in the YAML file.

   .. _cce_01_0288__table132326831016:

   .. table:: **Table 2** Description

      +----------------+-----------------------------------------------------------------------------------------+-----------+
      | Field          | Description                                                                             | Mandatory |
      +================+=========================================================================================+===========+
      | apiVersion     | API version. The value is **crd.yangtse.cni/v1**.                                       | Yes       |
      +----------------+-----------------------------------------------------------------------------------------+-----------+
      | kind           | Type of the object to be created.                                                       | Yes       |
      +----------------+-----------------------------------------------------------------------------------------+-----------+
      | metadata       | Metadata definition of the resource object.                                             | Yes       |
      +----------------+-----------------------------------------------------------------------------------------+-----------+
      | name           | Name of the SecurityGroup.                                                              | Yes       |
      +----------------+-----------------------------------------------------------------------------------------+-----------+
      | namespace      | Name of the namespace.                                                                  | Yes       |
      +----------------+-----------------------------------------------------------------------------------------+-----------+
      | Spec           | Detailed description of the SecurityGroup.                                              | Yes       |
      +----------------+-----------------------------------------------------------------------------------------+-----------+
      | podselector    | Used to define the workload to be associated with security groups in the SecurityGroup. | Yes       |
      +----------------+-----------------------------------------------------------------------------------------+-----------+
      | SecurityGroups | Security group ID.                                                                      | Yes       |
      +----------------+-----------------------------------------------------------------------------------------+-----------+

#. Run the following command to create the SecurityGroup:

   **kubectl create -f securitygroup-demo.yaml**

   If the following information is displayed, the SecurityGroup is being created.

   .. code-block::

      securitygroup.crd.yangtse.cni/demo created

#. Run the following command to view the SecurityGroup:

   **kubectl get sg**

   If the name of the created SecurityGroup is **demo** in the command output, the SecurityGroup is created successfully.

   .. code-block::

      NAME                       POD-SELECTOR                      AGE
      all-no                     map[matchLabels:map[app:nginx]]   4h1m
      s001test                   map[matchLabels:map[app:nginx]]   19m
      demo                       map[matchLabels:map[app:nginx]]   2m9s

Other Operations
----------------

.. table:: **Table 3** Other operations

   +-----------------------------------+-------------------------------------------------------------------------------------------------+
   | Operation                         | Procedure                                                                                       |
   +===================================+=================================================================================================+
   | Deletion                          | #. In the navigation pane of the CCE console, choose **Resource Management** > **Network**.     |
   |                                   | #. On the **SecurityGroup** tab page, select the target SecurityGroup.                          |
   |                                   | #. Click SecurityGroup to delete the SecurityGroup.                                             |
   +-----------------------------------+-------------------------------------------------------------------------------------------------+
   | Update                            | #. In the navigation pane of the CCE console, choose **Resource Management** > **Network**.     |
   |                                   |                                                                                                 |
   |                                   | #. On the **SecurityGroup** tab page, click **Update** at the same row as the SecurityGroup.    |
   |                                   |                                                                                                 |
   |                                   |    You can update the SecurityGroup ID and associated workload.                                 |
   +-----------------------------------+-------------------------------------------------------------------------------------------------+
   | Viewing the YAML file             | #. In the navigation pane of the CCE console, choose **Resource Management** > **Network**.     |
   |                                   |                                                                                                 |
   |                                   | #. On the **SecurityGroup** tab page, click **View YAML** at the same row as the SecurityGroup. |
   |                                   |                                                                                                 |
   |                                   |    You can view, copy, and download the YAML file.                                              |
   +-----------------------------------+-------------------------------------------------------------------------------------------------+
   | Viewing events                    | #. In the navigation pane of the CCE console, choose **Resource Management** > **Network**.     |
   |                                   |                                                                                                 |
   |                                   | #. On the **SecurityGroup** tab page, click **View Event**.                                     |
   |                                   |                                                                                                 |
   |                                   |    You can query the event information.                                                         |
   +-----------------------------------+-------------------------------------------------------------------------------------------------+
