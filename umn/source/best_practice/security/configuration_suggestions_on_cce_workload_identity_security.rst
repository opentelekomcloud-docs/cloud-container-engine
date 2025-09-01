:original_name: cce_bestpractice_0333.html

.. _cce_bestpractice_0333:

Configuration Suggestions on CCE Workload Identity Security
===========================================================

A workload identity enables workloads within a cluster to act as IAM users, granting them access to cloud services without the need for an IAM account's AK/SK. This helps minimize security risks.

This section describes how to use workload identities in CCE.

Notes and Constraints
---------------------

The cluster version must be v1.19.16 or later.

Procedure
---------

#. Obtain the public key of the cluster service account token from CCE. For details, see :ref:`Step 1: Obtain the Public Key for Signature of the CCE Cluster <cce_bestpractice_0333__en-us_topic_0000001280331044_section5937204594820>`.

#. Create an identity provider on IAM. For details, see :ref:`Step 2: Configure an Identity Provider <cce_bestpractice_0333__en-us_topic_0000001280331044_section18167152865013>`.

#. Obtain an IAM token from the workload and simulate an IAM user to access a cloud service. For details, see :ref:`Step 3: Use a Workload Identity <cce_bestpractice_0333__en-us_topic_0000001280331044_section38531454152611>`.

   The procedure is as follows:

   a. Deploy the application pod and obtain the OpenID Connect ID token file by mounting the identity provider.
   b. Use the mounted OpenID Connect ID token file in programs in the pod to access IAM and obtain a temporary IAM token.
   c. Access the cloud service using the IAM token in programs in the pod.


   .. figure:: /_static/images/en-us_image_0000002101396665.png
      :alt: **Figure 1** Workflow

      **Figure 1** Workflow

.. _cce_bestpractice_0333__en-us_topic_0000001280331044_section5937204594820:

Step 1: Obtain the Public Key for Signature of the CCE Cluster
--------------------------------------------------------------

#. Use kubectl to access the target cluster.

#. Obtain the public key:

   .. code-block::

      kubectl get --raw /openid/v1/jwks

   The returned result is the public key of the cluster. The following is an example of the command output:

   .. code-block::

      # kubectl get --raw /openid/v1/jwks
      {"keys":[{"use":"sig","kty":"RSA","kid":"*****","alg":"RS256","n":"*****","e":"AQAB"}]}

.. _cce_bestpractice_0333__en-us_topic_0000001280331044_section18167152865013:

Step 2: Configure an Identity Provider
--------------------------------------

#. Log in to the IAM console, choose **Identity Providers** in the navigation pane, and click **Create Identity Provider** in the upper right corner. On the displayed page, set **Protocol** to **OpenID Connect** and **SSO Type** to **Virtual user** and click **OK**.

#. In the identity provider list, locate the row containing the new identity provider and click **Modify** in the **Operation** column to modify the identity provider information.

   **Access Type**: Select **Programmatic access**.

   **Configuration Information**

   -  **Identity Provider URL**: Enter **https://kubernetes.default.svc.cluster.local**.
   -  **Client ID**: Enter a client ID, which will be used when you create a container.

      .. caution::

         A client ID cannot contain only digits. If the client ID consists only of digits, enclose it in double quotation marks ("") when editing the YAML file for the workload. For example, if the client ID is **123456789**, it should be entered as **"123456789"** in the YAML file.

   -  **Signing Key**: Enter the JWKS of the CCE cluster obtained in :ref:`Step 1: Obtain the Public Key for Signature of the CCE Cluster <cce_bestpractice_0333__en-us_topic_0000001280331044_section5937204594820>`.

   **Identity Conversion Rules**

   An identity conversion rule maps the ServiceAccount of a workload to an IAM user.

   For example, create a ServiceAccount named **oidc-token** in namespace **default** of the cluster and map it to user group **demo**. If you use the identity provider ID to access cloud services, you have the permissions of the **demo** user group. The attribute must be **sub**. The value is in the format of **system:serviceaccount:Namespace:ServiceAccountName**.

   Rules are in the JSON format as follows:

   .. code-block::

      [
          {
              "local": [
                  {
                      "user": {
                          "name": "test"
                      }
                  },
                  {
                      "group": {
                          "name": "demo"
                      }
                  }
              ],
              "remote": [
                  {
                      "type": "sub",
                      "any_one_of": [
                          "system:serviceaccount:default:oidc-token"
                      ]
                  }
              ]
          }
      ]

#. Click **OK**.

.. _cce_bestpractice_0333__en-us_topic_0000001280331044_section38531454152611:

Step 3: Use a Workload Identity
-------------------------------

#. Create a ServiceAccount, whose name must be the value of **ServiceAccountName** set in :ref:`Step 2: Configure an Identity Provider <cce_bestpractice_0333__en-us_topic_0000001280331044_section18167152865013>`.

   .. code-block::

      apiVersion: v1
      kind: ServiceAccount
      metadata:
        name: oidc-token

#. Mount the identity provider to the workload and obtain the OpenID Connect ID token file.

   An example is as follows:

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: nginx
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: nginx
            version: v1
        template:
          metadata:
            labels:
              app: nginx
              version: v1
          spec:
            containers:
            - name: container-1
              image: nginx:latest
              volumeMounts:
              - mountPath: "/var/run/secrets/tokens"     # Mount the service account token generated by Kubernetes to the /var/run/secrets/tokens/oidc-token file.
                name: oidc-token
            imagePullSecrets:
            - name: default-secret
            serviceAccountName: oidc-token      # Name of the created ServiceAccount
            volumes:
            - name: oidc-token
              projected:
                defaultMode: 420
                sources:
                - serviceAccountToken:
                    audience: client_id   # Must be the client ID of the identity provider.
                    expirationSeconds: 7200       # Expiry period
                    path: oidc-token              # Path name, which can be customized

#. After the creation is complete, log in to the container. The content of the **/var/run/secrets/tokens/oidc-token** file is the service account token generated by Kubernetes.

   .. note::

      If the service account token is used for more than 24 hours or 80% of its expiry period, kubelet will automatically rotate the service account token.

#. Use the OpenID Connect ID token to call the API for `Obtaining a Token with an OpenID Connect ID Token <https://docs.otc.t-systems.com/en-us/api/iam/iam_13_0605.html>`__. The **X-Subject-Token** field in the response header is the IAM token. Then, you can use this token to access cloud services.

   The following shows an example:

   .. code-block::

      curl -i --location --request POST 'https://{{iam endpoint}}/v3.0/OS-AUTH/id-token/tokens' \
       --header 'X-Idp-Id: workload_identity' \
       --header 'Content-Type: application/json' \
       --data @token_body.json

   Specifically:

   -  **{{iam endpoint}}** indicates the endpoint of IAM. For details, see `Regions and Endpoints <https://docs.otc.t-systems.com/regions-and-endpoints/index.html>`__.

   -  **workload_identity** is the identity provider name, which is the same as that configured in :ref:`Step 2: Configure an Identity Provider <cce_bestpractice_0333__en-us_topic_0000001280331044_section18167152865013>`.

   -  **token_body.json** is a local file and its content is as follows:

      .. code-block::

          {
            "auth" : {
              "id_token" : {
                "id" : "eyJhbGciOiJSU..."
              },
              "scope": {
                "project" : {
                  "id" : "46419baef4324...",
                  "name" : ******
                }
              }
            }
          }

      -  **$.auth.id_token.id**: The value is the content of the **/var/run/secrets/tokens/oidc-token** file in the container.
      -  **$.auth.scope.project.id**: indicates the project ID. To obtain the value, see `Obtaining a Project ID <https://docs.otc.t-systems.com/en-us/api2/cce/cce_02_0341.html>`__.
      -  **$.auth.scope.project.name**: indicates the project name.
