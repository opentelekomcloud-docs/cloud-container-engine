:original_name: cce_qs_0006.html

.. _cce_qs_0006:

Preparations
============

Before using CCE, make the following preparations:

-  :ref:`Creating an IAM user <cce_qs_0006__section929013341428>`
-  :ref:`Obtaining Resource Permissions <cce_qs_0006__section8819171411219>`
-  :ref:`(Optional) Creating a VPC <cce_qs_0006__section896411852619>`
-  :ref:`(Optional) Creating a Key Pair <cce_qs_0006__section2031413481717>`

.. _cce_qs_0006__section929013341428:

Creating an IAM user
--------------------

If you want to allow multiple users to manage your resources without sharing your password or private key, you can create users using IAM and grant permissions to the users. These users can use specified links and their own accounts and help you manage resources efficiently. You can also configure account security policies to ensure the security of these accounts.

Your accounts have the permissions to use CCE. However, IAM users created by your accounts do not have the permissions. You need to manually assign the permissions to IAM users.

.. _cce_qs_0006__section8819171411219:

Obtaining Resource Permissions
------------------------------

CCE works closely with multiple cloud services to support computing, storage, networking, and monitoring functions. When you log in to the CCE console for the first time, CCE automatically requests permissions to access those cloud services in the region where you run your applications. Specifically:

-  Compute services

   When you create a node in a cluster, a cloud server is created accordingly. The prerequisite is that CCE has obtained the permissions to access Elastic Cloud Service (ECS) and Bare Metal Server (BMS).

-  Storage services

   CCE allows you to mount storage volumes to nodes and containers in a cluster. The prerequisite is that CCE has obtained the permissions to access services such as Elastic Volume Service (EVS), Scalable File Service (SFS), and Object Storage Service (OBS).

-  Networking services

   CCE allows containers in a cluster to be published as services that can be accessed by external systems. The prerequisite is that CCE has obtained the permissions to access services such as Virtual Private Cloud (VPC) and Elastic Load Balance (ELB).

-  Container and monitoring services

   CCE supports functions such as container image pull, monitoring, and logging. The prerequisite is that CCE has obtained the permissions to access services such as SoftWare Repository for Container (SWR) and Application Operations Management (AOM).

After you agree to delegate the permissions, an agency named **cce_admin_trust** will be created for CCE in Identity and Access Management (IAM). The system account **op_svc_cce** will be delegated the **Tenant Administrator** role to perform operations on other cloud service resources. Tenant Administrator has the permissions on all cloud services except IAM, which calls the cloud services on which CCE depends. The delegation takes effect only in the current region. For details, see `Account Delegation <https://docs.otc.t-systems.com/en-us/usermanual/iam/iam_01_0054.html>`__.

To use CCE in multiple regions, request for cloud resource permissions in each region. You can go to the IAM console, choose **Agencies**, and click **cce_admin_trust** to view the delegation records of each region.

.. note::

   CCE may fail to run as expected if the Tenant Administrator role is not assigned. Therefore, do not delete or modify the **cce_admin_trust** agency when using CCE.

.. _cce_qs_0006__section896411852619:

(Optional) Creating a VPC
-------------------------

A VPC provides an isolated, configurable, and manageable virtual network for CCE clusters.

Before creating the first cluster, ensure that a VPC has been created.

If you already have a VPC available, skip this step.

#. Log in to the management console.

#. Click |image1| in the upper left corner and select a region and a project.

#. Under **Networking**, click **Virtual Private Cloud**.

#. Click **Create VPC**.

#. On the **Create VPC** page, configure parameters as prompted.

   A default subnet will be created together with a VPC. You can click **Add Subnet** to create more subnets for the VPC.

#. Click **Create Now**.

.. _cce_qs_0006__section2031413481717:

(Optional) Creating a Key Pair
------------------------------

The cloud platform uses public key cryptography to protect the login information of your CCE nodes. Passwords or key pairs are used for identity authentication during remote login to nodes.

-  If you choose the key pair login mode, you need to specify the key pair name when creating a node and provide the private key when logging to the node using SSH.
-  If you choose the password login mode, skip this task.

.. note::

   If you want to create pods in multiple regions, you need to create a key pair in each region.

**Creating a Key Pair on the Management Console**

If you have no key pair, create one on the management console. The procedure is as follows:

#. Log in to the management console.
#. Click |image2| in the upper left corner and select a region and a project.
#. Under **Compute**, click **Elastic Cloud Server**.
#. In the navigation pane, choose **Key Pair**.
#. On the displayed page, click **Create Key Pair**.
#. Enter the key pair name and click **OK**.
#. A key pair name consists **KeyPair** and four random digits. You can enter an easy-to-remember name, for example, **KeyPair-xxxx_ecs**.
#. Manually or automatically download the private key file. The file name is a specified key pair name with a suffix of .pem. Securely store the private key file. In the dialog box displayed, click **OK**.

   .. note::

      The private key file can be downloaded only once. Keep it secure. When creating an ECS, provide the name of your desired key pair. Each time you SSH into the ECS, provide the private key.

.. |image1| image:: /_static/images/en-us_image_0000001798307873.png
.. |image2| image:: /_static/images/en-us_image_0000001751467114.png
