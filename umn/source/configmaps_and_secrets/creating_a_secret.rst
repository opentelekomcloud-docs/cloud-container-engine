:original_name: cce_10_0153.html

.. _cce_10_0153:

Creating a Secret
=================

Scenario
--------

A secret is a type of resource that holds sensitive data, such as authentication and key information. Its content is user-defined. After creating secrets, you can use them as files or environment variables in a containerized workload.

Notes and Constraints
---------------------

Secrets cannot be used in `static pods <https://kubernetes.io/docs/tasks/configure-pod-container/static-pod/>`__.

Creating a Key Pair using the Console
-------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. Choose **ConfigMaps and Secrets** in the navigation pane, click the **Secrets** tab, and click **Create Secret** in the upper right corner.

#. Configure parameters.

   .. _cce_10_0153__table16321825732:

   .. table:: **Table 1** Parameters for creating a secret

      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                                                                      |
      +===================================+==================================================================================================================================================================================================================================================================================================================+
      | Name                              | Name of the secret you create, which must be unique.                                                                                                                                                                                                                                                             |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Namespace                         | Namespace to which the secret belongs. If you do not specify this parameter, the value **default** is used by default.                                                                                                                                                                                           |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Description                       | Description of a secret.                                                                                                                                                                                                                                                                                         |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Secret Type                       | Type of the secret you create.                                                                                                                                                                                                                                                                                   |
      |                                   |                                                                                                                                                                                                                                                                                                                  |
      |                                   | -  Opaque: common secret.                                                                                                                                                                                                                                                                                        |
      |                                   | -  kubernetes.io/dockerconfigjson: a secret that stores the authentication information required for pulling images from a private repository.                                                                                                                                                                    |
      |                                   | -  **kubernetes.io/tls**: Kubernetes TLS secret, which is used to store the certificate required by layer-7 load balancing Services. For details about examples of the kubernetes.io/tls secret and its description, see `TLS secrets <https://kubernetes.io/docs/concepts/configuration/secret/#tls-secret>`__. |
      |                                   | -  **IngressTLS**: TLS secret provided by CCE to store the certificate required by layer-7 load balancing Services.                                                                                                                                                                                              |
      |                                   | -  Other: another type of secret, which is specified manually.                                                                                                                                                                                                                                                   |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Data                              | Workload secret data can be used in containers.                                                                                                                                                                                                                                                                  |
      |                                   |                                                                                                                                                                                                                                                                                                                  |
      |                                   | -  If **Secret Type** is set to **Opaque**, click **Add**. In the window that slides out from the right, enter a key-value pair and select **Auto Base64 Encoding**.                                                                                                                                             |
      |                                   |                                                                                                                                                                                                                                                                                                                  |
      |                                   |    **Key**: Enter 1 to 63 characters. Only digits, letters, periods (.), hyphens (-), and underscores (_) are allowed. The key cannot start with two periods (..).                                                                                                                                               |
      |                                   |                                                                                                                                                                                                                                                                                                                  |
      |                                   | -  If **Secret Type** is set to **kubernetes.io/dockerconfigjson**, click **Add** and enter the account and password for logging in to the private image repository.                                                                                                                                             |
      |                                   |                                                                                                                                                                                                                                                                                                                  |
      |                                   | -  If **Secret Type** is **kubernetes.io/tls** or **IngressTLS**, upload the certificate file and private key file.                                                                                                                                                                                              |
      |                                   |                                                                                                                                                                                                                                                                                                                  |
      |                                   |    .. note::                                                                                                                                                                                                                                                                                                     |
      |                                   |                                                                                                                                                                                                                                                                                                                  |
      |                                   |       -  A certificate is a self-signed or CA-signed credential used for identity authentication.                                                                                                                                                                                                                |
      |                                   |       -  A certificate request is a request for a signature with a private key.                                                                                                                                                                                                                                  |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Secret Label                      | Label of the secret. Click **Add Label** and enter key-value pairs. The key and value must contain 1 to 63 characters that start and end with a letter or digit. Only letters, digits, hyphens (-), underscores (_), and periods (.) are allowed.                                                                |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. Click **OK**.

   The new secret is displayed in the key list.

Creating a Secret Using kubectl
-------------------------------

#. Use kubectl to access the cluster. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

#. Encode the secret value using Base64.

   .. code-block::

      # echo -n "content-to-be-encoded" | base64
      ******

#. Create a file named **cce-secret.yaml** and edit it.

   .. code-block::

      vi cce-secret.yaml

   The following YAML file uses the Opaque type as an example. For details about other types, see :ref:`Secret Resource File Configuration Example <cce_10_0153__section187197531454>`.

   .. code-block::

      apiVersion: v1
      kind: Secret
      metadata:
        name: mysecret
      type: Opaque
      data:
        <your_key>: <your_value>  # Enter a key-value pair. The value must be encoded using Base64.

#. Create a secret.

   .. code-block::

      kubectl create -f cce-secret.yaml

   You can query the secret after creation.

   .. code-block::

      kubectl get secret -n default

.. _cce_10_0153__section187197531454:

Secret Resource File Configuration Example
------------------------------------------

This section describes configuration examples of secret resource description files.

-  Opaque type

   The **secret.yaml** file is defined as shown below. The **data** field is filled in as a key-value pair, and the **value** field must be encoded using Base64. For details, see :ref:`Base64 Encoding <cce_10_0153__section95945576477>`.

   .. code-block::

      apiVersion: v1
      kind: Secret
      metadata:
        name: mysecret           #Secret name
        namespace: default       #Namespace. The default value is default.
      data:
        <your_key>: <your_value>  # Enter a key-value pair. The value must be encoded using Base64.
      type: Opaque

-  kubernetes.io/dockerconfigjson type

   The **secret.yaml** file is defined as shown below. The value of **.dockerconfigjson** must be encoded using Base64. For details, see :ref:`Base64 Encoding <cce_10_0153__section95945576477>`.

   .. code-block::

      apiVersion: v1
      kind: Secret
      metadata:
        name: mysecret           #Secret name
        namespace: default       #Namespace. The default value is default.
      data:
        .dockerconfigjson: eyJh*****    # Content encoded using Base64.
      type: kubernetes.io/dockerconfigjson

   To obtain the **.dockerconfigjson** content, perform the following steps:

   #. Obtain the following login information of the image repository.

      -  Image repository address: The section uses *address* as an example. Replace it with the actual address.
      -  Username: The section uses *username* as an example. Replace it with the actual username.
      -  Password: The section uses *password* as an example. Replace it with the actual password.

   #. Use Base64 to encode the key-value pair *username:password* and fill the encoded content in :ref:`3 <cce_10_0153__li157901847113720>`.

      .. code-block::

         echo -n "username:password" | base64

      Command output:

      .. code-block::

         dXNlcm5hbWU6cGFzc3dvcmQ=

   #. .. _cce_10_0153__li157901847113720:

      Use Base64 to encode the following JSON content:

      .. code-block::

         echo -n '{"auths":{"address":{"username":"username","password":"password","auth":"dXNlcm5hbWU6cGFzc3dvcmQ="}}}' | base64

      Command output:

      .. code-block::

         eyJhdXRocyI6eyJhZGRyZXNzIjp7InVzZXJuYW1lIjoidXNlcm5hbWUiLCJwYXNzd29yZCI6InBhc3N3b3JkIiwiYXV0aCI6ImRYTmxjbTVoYldVNmNHRnpjM2R2Y21RPSJ9fX0=

      The encoded content is the **.dockerconfigjson** content.

-  kubernetes.io/tls type

   The value of **tls.crt** and **tls.key** must be encoded using Base64. For details, see :ref:`Base64 Encoding <cce_10_0153__section95945576477>`.

   .. code-block::

      kind: Secret
      apiVersion: v1
      metadata:
        name: mysecret           #Secret name
        namespace: default       #Namespace. The default value is default.
      data:
        tls.crt: LS0tLS1CRU*****FURS0tLS0t  # Certificate content, which must be encoded using Base64.
        tls.key: LS0tLS1CRU*****VZLS0tLS0=  # Private key content, which must be encoded using Base64.
      type: kubernetes.io/tls

-  IngressTLS type

   The value of **tls.crt** and **tls.key** must be encoded using Base64. For details, see :ref:`Base64 Encoding <cce_10_0153__section95945576477>`.

   .. code-block::

      kind: Secret
      apiVersion: v1
      metadata:
        name: mysecret           #Secret name
        namespace: default       #Namespace. The default value is default.
      data:
        tls.crt: LS0tLS1CRU*****FURS0tLS0t  # Certificate content, which must be encoded using Base64.
        tls.key: LS0tLS1CRU*****VZLS0tLS0=  # Private key content, which must be encoded using Base64.
      type: IngressTLS

.. _cce_10_0153__section95945576477:

Base64 Encoding
---------------

To perform Base64 encoding on a string, run the following command:

.. code-block::

   echo -n "Content to be encoded" | base64

Related Operations
------------------

After creating a secret, you can update or delete it as described in :ref:`Table 2 <cce_10_0153__table555785274319>`.

.. note::

   The secret list contains system secret resources that can be queried only. The system secret resources cannot be updated or deleted.

.. _cce_10_0153__table555785274319:

.. table:: **Table 2** Related operations

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
