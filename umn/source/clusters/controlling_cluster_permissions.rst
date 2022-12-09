:original_name: cce_01_0085.html

.. _cce_01_0085:

Controlling Cluster Permissions
===============================

Scenario
--------

This section describes how to control permissions on resources in a cluster, for example, allow user A to read and write application data in a namespace, and user B to only read resource data in a cluster.

Procedure
---------

#. If you need to perform permission control on the cluster, select **Enhanced authentication** for **Authentication Mode** during cluster creation, upload your own **CA certificate**, **client certificate**, and **client certificate private key** (for details about how to create a certificate, see `Certificates <https://kubernetes.io/docs/tasks/administer-cluster/certificates/>`__), and select **I have confirmed that the uploaded certificates are valid**. For details, see :ref:`Table 1 <cce_01_0028__table8638121213265>`.

   .. caution::

      -  Upload a file **smaller than 1 MB**. The CA certificate and client certificate can be in **.crt** or **.cer** format. The private key of the client certificate can only be uploaded **unencrypted**.
      -  The validity period of the client certificate must be longer than five years.
      -  The uploaded CA certificate is used for both the authentication proxy and the kube-apiserver aggregation layer configuration. **If the certificate is invalid, the cluster cannot be created**.

#. Create a role using kubectl.

   The following example shows how to create a **role** and allow the role to read all pods in the default namespace. For details about the parameters, see the `official Kubernetes documentation <https://kubernetes.io/docs/reference/>`__.

   .. code-block::

      kind: Role
      apiVersion: rbac.authorization.k8s.io/v1
      metadata:
        namespace: default
        name: pod-reader
      rules:
      - apiGroups: [""]
        resources: ["pods"]
        verbs: ["get", "watch", "list"]

#. Bind the role to a user by using kubectl.

   In the following example, the **RoleBinding** assigns the role of **pod-reader** in the default namespace to user **jane**. This policy allows user **jane** to read all pods in the default namespace. For details about the parameters, see the `official Kubernetes documentation <https://kubernetes.io/docs/reference/>`__.

   .. code-block::

      kind: RoleBinding
      apiVersion: rbac.authorization.k8s.io/v1
      metadata:
        name: read-pods
        namespace: default
      subjects:
      - kind: User
        name: jane   #User name
        apiGroup: rbac.authorization.k8s.io
      roleRef:
        kind: Role
        name: pod-reader    #Name of the role that is created
        apiGroup: rbac.authorization.k8s.io

#. After a role is created and bound to a user, call a Kubernetes API by initiating an API request message where headers carry user information and the certificate uploaded during cluster creation. For example, to call the pod query API, run the following command:

   **curl -k -H "X-Remote-User: jane" --cacert /root/tls-ca.crt --key /root/tls.key --cert /root/tls.crt https://**\ *192.168.23.5:5443*\ **/api/v1/namespaces/default/pods**

   If **200** is returned, user **jane** is authorized to read pods in the cluster's default namespace. If **403** is returned, user **jane** is not authorized to read pods in the cluster's default namespace.

   .. note::

      To prevent the command execution failure, upload the certificate to the **/root** directory in advance.

   The parameter descriptions are as follows:

   -  **X-Remote-User: jane**: The request header is fixed at **X-Remote-User**, and **jane** is the username.

   -  **tls-ca.crt**: CA root certificate uploaded during cluster creation.

   -  **tls.crt**: client certificate that matches the CA root certificate uploaded during cluster creation.

   -  **tls.key**: client key corresponding to the CA root certificate uploaded during cluster creation.

   -  **192.168.23.5:5443**: address for connecting to the cluster. To obtain the address, perform the following steps:

      Log in to the CCE console. In the navigation pane, choose **Resource Management > Clusters**. Click the name of the cluster to be connected and obtain the IP address and port number from **Internal API Server Address** on the cluster details page.


      .. figure:: /_static/images/en-us_image_0000001144208440.png
         :alt: **Figure 1** Obtaining the access address

         **Figure 1** Obtaining the access address

   In addition, the **X-Remote-Group** header field, that is, the user group name, is supported. During role binding, a role can be bound to a group and carry user group information when you access the cluster.
