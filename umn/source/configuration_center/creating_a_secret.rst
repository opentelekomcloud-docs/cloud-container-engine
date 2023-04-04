:original_name: cce_10_0153.html

.. _cce_10_0153:

Creating a Secret
=================

Scenario
--------

A secret is a type of resource that holds sensitive data, such as authentication and key information. Its content is user-defined. After creating secrets, you can use them as files or environment variables in a containerized workload.

Procedure
---------

#. Log in to the CCE console and access the cluster console.

#. Choose **ConfigMaps and Secrets** in the navigation pane, click the **Secrets** tab, and click **Create Secret** in the upper right corner.

#. Set parameters.

   .. _cce_10_0153__table16321825732:

   .. table:: **Table 1** Parameters for creating a secret

      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                   |
      +===================================+===============================================================================================================================================+
      | Name                              | Name of the secret you create, which must be unique.                                                                                          |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
      | Namespace                         | Namespace to which the secret belongs. If you do not specify this parameter, the value **default** is used by default.                        |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
      | Description                       | Description of a secret.                                                                                                                      |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
      | Type                              | Type of the secret you create.                                                                                                                |
      |                                   |                                                                                                                                               |
      |                                   | -  Opaque: common secret.                                                                                                                     |
      |                                   | -  kubernetes.io/dockerconfigjson: a secret that stores the authentication information required for pulling images from a private repository. |
      |                                   | -  IngressTLS: a secret that stores the certificate required by ingresses (layer-7 load balancing Services).                                  |
      |                                   | -  Other: another type of secret, which is specified manually.                                                                                |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
      | Secret Data                       | Workload secret data can be used in containers.                                                                                               |
      |                                   |                                                                                                                                               |
      |                                   | -  If **Secret Type** is **Opaque**, click |image1|. In the dialog box displayed, enter a key-value pair and select **Auto Base64 Encoding**. |
      |                                   | -  If the secret type is kubernetes.io/dockerconfigjson, enter the account and password of the private image repository.                      |
      |                                   | -  If the secret type is IngressTLS, upload the certificate file and private key file.                                                        |
      |                                   |                                                                                                                                               |
      |                                   |    .. note::                                                                                                                                  |
      |                                   |                                                                                                                                               |
      |                                   |       -  A certificate is a self-signed or CA-signed credential used for identity authentication.                                             |
      |                                   |       -  A certificate request is a request for a signature with a private key.                                                               |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+
      | Secret Label                      | Label of the secret. Enter a key-value pair and click **Add**.                                                                                |
      +-----------------------------------+-----------------------------------------------------------------------------------------------------------------------------------------------+

#. After the configuration is complete, click **OK**.

   The new secret is displayed in the key list.

Secret Resource File Configuration
----------------------------------

This section describes configuration examples of secret resource description files.

For example, you can retrieve the username and password for a workload through a secret.

-  YAML format

   The **secret.yaml** file is defined as shown below. The value must be based on the Base64 coding method. For details about the method, see :ref:`Base64 Encoding <cce_10_0153__section175000605919>`.

   .. code-block::

      apiVersion: v1
      kind: Secret
      metadata:
        name: mysecret           #Secret name
        namespace: default       #Namespace. The default value is default.
      data:
        username: ******  #The value must be Base64-encoded.
        password: ******  #The value must be encoded using Base64.
      type: Opaque     #You are advised not to change this parameter value.

Creating a Secret Using kubectl
-------------------------------

#. According to :ref:`Connecting to a Cluster Using kubectl <cce_10_0107>`, configure the **kubectl** command to connect an ECS to the cluster.

#. Create and edit the Base64-encoded **cce-secret.yaml** file.

   .. code-block::

      # echo -n "content to be encoded" | base64
      ******

   **vi cce-secret.yaml**

   .. code-block::

      apiVersion: v1
      kind: Secret
      metadata:
        name: mysecret
      type: Opaque
      data:
        username: ******
        password: ******

#. Create a secret.

   **kubectl create -f cce-secret.yaml**

   You can query the secret after creation.

   **kubectl get secret**

Related Operations
------------------

After creating a secret, you can update or delete it as described in :ref:`Table 2 <cce_10_0153__table555785274319>`.

.. note::

   The secret list contains system secret resources that can be queried only. The system secret resources cannot be updated or deleted.

.. _cce_10_0153__table555785274319:

.. table:: **Table 2** Related Operations

   +-----------------------------------+------------------------------------------------------------------------------------------------------+
   | Operation                         | Description                                                                                          |
   +===================================+======================================================================================================+
   | Editing a YAML file               | Click **Edit YAML** in the row where the target secret resides to edit its YAML file.                |
   +-----------------------------------+------------------------------------------------------------------------------------------------------+
   | Updating a secret                 | #. Select the name of the secret to be updated and click **Update**.                                 |
   |                                   | #. Modify the secret data. For more information, see :ref:`Table 1 <cce_10_0153__table16321825732>`. |
   |                                   | #. Click **OK**.                                                                                     |
   +-----------------------------------+------------------------------------------------------------------------------------------------------+
   | Deleting a secret                 | Select the secret you want to delete and click **Delete**.                                           |
   |                                   |                                                                                                      |
   |                                   | Follow the prompts to delete the secret.                                                             |
   +-----------------------------------+------------------------------------------------------------------------------------------------------+
   | Deleting secrets in batches       | #. Select the secrets to be deleted.                                                                 |
   |                                   | #. Click **Delete** above the secret list.                                                           |
   |                                   | #. Follow the prompts to delete the secrets.                                                         |
   +-----------------------------------+------------------------------------------------------------------------------------------------------+

.. _cce_10_0153__section175000605919:

Base64 Encoding
---------------

To Base64-encode a string, run the **echo -n content to be encoded \| base64** command. The following is an example:

.. code-block::

   root@ubuntu:~# echo -n "content to be encoded" | base64
   ******

.. |image1| image:: /_static/images/en-us_image_0000001249958645.png
