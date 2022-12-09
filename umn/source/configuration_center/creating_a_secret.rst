:original_name: cce_01_0153.html

.. _cce_01_0153:

Creating a Secret
=================

Scenario
--------

A secret is a type of resource that holds sensitive data, such as authentication and key information. Its content is user-defined. After creating secrets, you can use them as files or environment variables in a containerized workload.

Prerequisites
-------------

Cluster and node resources have been created. For more information, see :ref:`Creating a CCE Cluster <cce_01_0028>`. If you have available clusters and node resources, skip this operation.

Procedure
---------

#. Log in to the CCE console. In the navigation pane, choose **Configuration Center** > **Secrets**. Click **Create Secret**.

#. You can create a secret directly or based on YAML. If you want to create a secret based on YAML, go to :ref:`4 <cce_01_0153__li69121840101813>`.

#. Method 1: Create a secret directly.

   Set the basic information by referring to :ref:`Table 1 <cce_01_0153__table16321825732>`.

   .. _cce_01_0153__table16321825732:

   .. table:: **Table 1** Parameters for creating a secret

      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                      |
      +===================================+==================================================================================================================================================================================+
      | Name                              | Name of the secret you create, which must be unique.                                                                                                                             |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Cluster                           | Cluster that will use the secret you create.                                                                                                                                     |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Namespace                         | Namespace to which the secret belongs. If you do not specify this parameter, the value **default** is used by default.                                                           |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Description                       | Description of a secret.                                                                                                                                                         |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Type                              | Type of the secret you create.                                                                                                                                                   |
      |                                   |                                                                                                                                                                                  |
      |                                   | -  Opaque: common secret.                                                                                                                                                        |
      |                                   | -  kubernetes.io/dockerconfigjson: a secret that stores the authentication information required for pulling images from a private repository.                                    |
      |                                   | -  IngressTLS: a secret that stores the certificate required by ingresses (layer-7 load balancing Services).                                                                     |
      |                                   | -  Other: another type of secret, which is specified manually.                                                                                                                   |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Secret Data                       | Workload secret data can be used in containers.                                                                                                                                  |
      |                                   |                                                                                                                                                                                  |
      |                                   | -  If the secret is of the Opaque type:                                                                                                                                          |
      |                                   |                                                                                                                                                                                  |
      |                                   |    a. Click **Add Data**.                                                                                                                                                        |
      |                                   |    b. Enter the key and value. The value must be based on the Base64 coding method. For details about the method, see :ref:`Base64 Encoding <cce_01_0153__section175000605919>`. |
      |                                   |                                                                                                                                                                                  |
      |                                   | -  If the secret type is kubernetes.io/dockerconfigjson, enter the account and password of the private image repository.                                                         |
      |                                   | -  If the secret type is IngressTLS, upload the certificate file and private key file.                                                                                           |
      |                                   |                                                                                                                                                                                  |
      |                                   |    .. note::                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                  |
      |                                   |       -  A certificate is a self-signed or CA-signed credential used for identity authentication.                                                                                |
      |                                   |       -  A certificate request is a request for a signature with a private key.                                                                                                  |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Secret Label                      | Labels are attached to objects such as workloads, nodes, and Services in key-value pairs.                                                                                        |
      |                                   |                                                                                                                                                                                  |
      |                                   | Labels define the identifiable attributes of these objects and are used to manage and select the objects.                                                                        |
      |                                   |                                                                                                                                                                                  |
      |                                   | a. Click **Add Label**.                                                                                                                                                          |
      |                                   | b. Enter the key and value.                                                                                                                                                      |
      +-----------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. .. _cce_01_0153__li69121840101813:

   Method 2: Create a secret based on the YAML file.

   .. note::

      To create a resource by uploading a file, ensure that the resource description file has been created. CCE supports files in JSON or YAML format. For more information, see :ref:`Secret Resource File Configuration <cce_01_0153__section187197531454>`.

   You can import or directly write the file content in YAML or JSON format.

   -  Method 1: Import the orchestration file.

      Click **Add File** to import the file in YAML or JSON format. The orchestration content can be directly displayed.

   -  Method 2: Directly orchestrate the content.

      In the orchestration content area, enter the content of the YAML or JSON file.

#. After the configuration is complete, click **Create**.

   The new secret is displayed in the key list.

.. _cce_01_0153__section187197531454:

Secret Resource File Configuration
----------------------------------

This section describes configuration examples of secret resource description files.

For example, you can retrieve the username and password for a workload through a secret.

-  YAML format

   The **secret.yaml** file is defined as shown below. The value must be based on the Base64 coding method. For details about the method, see :ref:`Base64 Encoding <cce_01_0153__section175000605919>`.

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

.. _cce_01_0153__section821112149514:

Creating a Secret Using kubectl
-------------------------------

#. According to :ref:`Connecting to a Cluster Using kubectl <cce_01_0107>`, configure the **kubectl** command to connect an ECS to the cluster.

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

After creating a secret, you can update or delete it as described in :ref:`Table 2 <cce_01_0153__table555785274319>`.

.. note::

   The secret list contains system secret resources that can be queried only. The system secret resources cannot be updated or deleted.

.. _cce_01_0153__table555785274319:

.. table:: **Table 2** Related Operations

   +-----------------------------------+--------------------------------------------------------------------------------------------------------+
   | Operation                         | Description                                                                                            |
   +===================================+========================================================================================================+
   | Viewing a YAML file               | Click **View YAML** next to the secret name to view the YAML file corresponding to the current secret. |
   +-----------------------------------+--------------------------------------------------------------------------------------------------------+
   | Updating a secret                 | #. Select the name of the secret to be updated and click **Update**.                                   |
   |                                   | #. Modify the secret data. For more information, see :ref:`Table 1 <cce_01_0153__table16321825732>`.   |
   |                                   | #. Click **Update**.                                                                                   |
   +-----------------------------------+--------------------------------------------------------------------------------------------------------+
   | Deleting a secret                 | Select the secret you want to delete and click **Delete**.                                             |
   |                                   |                                                                                                        |
   |                                   | Follow the prompts to delete the secret.                                                               |
   +-----------------------------------+--------------------------------------------------------------------------------------------------------+
   | Deleting secrets in batches       | #. Select the secrets to be deleted.                                                                   |
   |                                   | #. Click **Delete** above the secret list.                                                             |
   |                                   | #. Follow the prompts to delete the secrets.                                                           |
   +-----------------------------------+--------------------------------------------------------------------------------------------------------+

.. _cce_01_0153__section175000605919:

Base64 Encoding
---------------

To encrypt a character string using Base64, run the **echo -n to-be-encoded content \| base64** command. The following is an example.

.. code-block::

   root@ubuntu:~# echo -n "content to be encoded" | base64
   ******
