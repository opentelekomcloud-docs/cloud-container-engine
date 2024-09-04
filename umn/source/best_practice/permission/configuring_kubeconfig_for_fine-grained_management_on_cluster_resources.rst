:original_name: cce_bestpractice_00221.html

.. _cce_bestpractice_00221:

Configuring kubeconfig for Fine-Grained Management on Cluster Resources
=======================================================================

Application Scenarios
---------------------

By default, the kubeconfig file provided by CCE for users has permissions bound to the **cluster-admin** role, which are equivalent to the permissions of user **root**. It is difficult to implement refined management on users with such permissions.

Purpose
-------

Cluster resources are managed in a refined manner so that specific users have only certain permissions (such as adding, querying, and modifying resources).

Precautions
-----------

Ensure that kubectl is available on your host. If not, download it from `here <https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/README.md>`__ (corresponding to the cluster version or the latest version).

Configuration Method
--------------------

.. note::

   In the following example, only pods and Deployments in the **test** space can be viewed and added, and they cannot be deleted.

#. Set the service account name to **my-sa** and namespace to **test**.

   .. code-block::

      kubectl create sa my-sa -n test

   |image1|

#. Configure the role table and assign operation permissions to different resources.

   .. code-block::

      vi role-test.yaml

   The content is as follows:

   .. note::

      In this example, the permission rules include the read-only permission (get/list/watch) of pods in the **test** namespace, and the read (get/list/watch) and create permissions of deployments.

   .. code-block::

      apiVersion: rbac.authorization.k8s.io/v1
      kind: Role
      metadata:
        annotations:
          rbac.authorization.kubernetes.io/autoupdate: "true"
        labels:
          kubernetes.io/bootstrapping: rbac-defaults
        name: myrole
        namespace: test
      rules:
      - apiGroups:
        - ""
        resources:
        - pods
        verbs:
        - get
        - list
        - watch
      - apiGroups:
        - apps
        resources:
        - pods
        - deployments
        verbs:
        - get
        - list
        - watch
        - create

   Create a Role.

   .. code-block::

      kubectl create -f role-test.yaml

   |image2|

#. Create a RoleBinding and bind the service account to the role so that the user can obtain the corresponding permissions.

   .. code-block::

      vi myrolebinding.yaml

   The content is as follows:

   .. code-block::

      apiVersion: rbac.authorization.k8s.io/v1
      kind: RoleBinding
      metadata:
        name: myrolebinding
        namespace: test
      roleRef:
        apiGroup: rbac.authorization.k8s.io
        kind: Role
        name: myrole
      subjects:
      - kind: ServiceAccount
        name: my-sa
        namespace: test

   Create a RoleBinding.

   .. code-block::

      kubectl create -f myrolebinding.yaml

   |image3|

   The user information is configured. Now perform :ref:`5 <cce_bestpractice_00221__en-us_topic_0235296162_li756812692518>` to :ref:`7 <cce_bestpractice_00221__en-us_topic_0235296162_li147965421277>` to write the user information to the configuration file.

#. Manually create a token that is valid for a long time for ServiceAccount.

   .. code-block::

      vi my-sa-token.yaml

   The content is as follows:

   .. code-block::

      apiVersion: v1
      kind: Secret
      metadata:
        name: my-sa-token-secret
        namespace: test
        annotations:
          kubernetes.io/service-account.name: my-sa
      type: kubernetes.io/service-account-token

   Create a token:

   .. code-block::

      kubectl create -f my-sa-token.yaml

#. .. _cce_bestpractice_00221__en-us_topic_0235296162_li756812692518:

   Configure the cluster information.

   a. Decrypt the **ca.crt** file in the secret and export it.

   .. code-block::

      kubectl get secret my-sa-token-secret -n test -oyaml |grep ca.crt: | awk '{print $2}' |base64 -d > /home/ca.crt

   b. Set a cluster access mode. **test-arm** specifies the cluster to be accessed. **https://192.168.0.110:5443** specifies the apiserver IP address of the cluster. **/home/test.config** specifies the path for storing the configuration file.

      -  If the internal API server address is used, run the following command:

         .. code-block::

            kubectl config set-cluster test-arm --server=https://192.168.0.110:5443  --certificate-authority=/home/ca.crt  --embed-certs=true --kubeconfig=/home/test.config

      -  If the public API server address is used, run the following command:

         .. code-block::

            kubectl config set-cluster test-arm --server=https://192.168.0.110:5443 --kubeconfig=/home/test.config --insecure-skip-tls-verify=true

      |image4|

   .. note::

      If you **perform operations on a node in the cluster** or **the node that uses the configuration is a cluster node**, do not set the path of kubeconfig to **/root/.kube/config**.

   By default, the apiserver IP address of the cluster is a private IP address. After an EIP is bound, you can use the public network IP address to access the apiserver.

#. Configure the cluster authentication information.

   a. Obtain the cluster token. (If the token is obtained in GET mode, run **based64 -d** to decode the token.)

   .. code-block::

      token=$(kubectl describe secret my-sa-token-secret -n test | awk '/token:/{print $2}')

   b. Set the cluster user **ui-admin**.

   .. code-block::

      kubectl config set-credentials ui-admin --token=$token --kubeconfig=/home/test.config

   |image5|

#. .. _cce_bestpractice_00221__en-us_topic_0235296162_li147965421277:

   Configure the context information for cluster authentication access. **ui-admin@test** specifies the context name.

   .. code-block::

      kubectl config set-context ui-admin@test --cluster=test-arm --user=ui-admin --kubeconfig=/home/test.config

   |image6|

#. .. _cce_bestpractice_00221__en-us_topic_0235296162_li1088912408273:

   Configure the context. For details about how to use the context, see :ref:`Verification <cce_bestpractice_00221__en-us_topic_0235296162_section14884146153319>`.

   .. code-block::

      kubectl config use-context ui-admin@test --kubeconfig=/home/test.config

   |image7|

   .. note::

      If you want to assign other users the above permissions to perform operations on the cluster, provide the generated configuration file **/home/test.config** to the user after performing step :ref:`7 <cce_bestpractice_00221__en-us_topic_0235296162_li147965421277>`. The user must ensure that the host can access the API server address of the cluster. When performing step :ref:`8 <cce_bestpractice_00221__en-us_topic_0235296162_li1088912408273>` on the host and using kubectl, the user must set the kubeconfig parameter to the path of the configuration file.

.. _cce_bestpractice_00221__en-us_topic_0235296162_section14884146153319:

Verification
------------

#. Pods in the **test** namespace cannot access pods in other namespaces.

   .. code-block::

      kubectl get pod -n test --kubeconfig=/home/test.config

   |image8|

#. Pods in the **test** namespace cannot be deleted.

   |image9|

Further Readings
----------------

For more information about users and identity authentication in Kubernetes, see `Authenticating <https://kubernetes.io/docs/reference/access-authn-authz/authentication/>`__.

.. |image1| image:: /_static/images/en-us_image_0000001981434377.png
.. |image2| image:: /_static/images/en-us_image_0000001950315012.png
.. |image3| image:: /_static/images/en-us_image_0000001981274533.png
.. |image4| image:: /_static/images/en-us_image_0000001950314996.png
.. |image5| image:: /_static/images/en-us_image_0000001981434361.png
.. |image6| image:: /_static/images/en-us_image_0000001950315008.png
.. |image7| image:: /_static/images/en-us_image_0000001981274513.png
.. |image8| image:: /_static/images/en-us_image_0000001981434389.png
.. |image9| image:: /_static/images/en-us_image_0000001981434369.png
