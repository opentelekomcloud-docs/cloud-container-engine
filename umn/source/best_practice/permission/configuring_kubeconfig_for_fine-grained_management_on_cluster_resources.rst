:original_name: cce_bestpractice_00221.html

.. _cce_bestpractice_00221:

Configuring kubeconfig for Fine-Grained Management on Cluster Resources
=======================================================================

Scenario
--------

By default, the kubeconfig file provided by CCE for users has permissions bound to the **cluster-admin** role, which are equivalent to the permissions of user **root**. It is difficult to implement refined management on users with such permissions.

Purpose
-------

Cluster resources are managed in a refined manner so that specific users have only certain permissions (such as adding, querying, and modifying resources).

Note
----

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

   The user information is configured. Now perform :ref:`4 <cce_bestpractice_00221__en-us_topic_0235296162_li756812692518>` to :ref:`6 <cce_bestpractice_00221__en-us_topic_0235296162_li147965421277>` to write the user information to the configuration file.

#. .. _cce_bestpractice_00221__en-us_topic_0235296162_li756812692518:

   Configure the cluster information.

   a. Use the sa name **my-sa** to obtain the secret corresponding to the sa. In the following example, **my-sa-token-z4967** in the first column is the secret name.

   .. code-block::

      kubectl get secret -n test |grep my-sa

   |image4|

   b. Decrypt the **ca.crt** file in the secret and export it.

   .. code-block::

      kubectl get secret my-sa-token-5gpl4 -n test -oyaml |grep ca.crt: | awk '{print $2}' |base64 -d > /home/ca.crt

   c. Set the cluster access mode. **test-arm** indicates the cluster to be accessed, **10.0.1.100** indicates the IP address of the API server in the cluster and **/home/test.config** indicates the path for storing the configuration file.

      -  If the internal API server address is used, run the following command:

         .. code-block::

            kubectl config set-cluster test-arm --server=https://10.0.1.100:5443  --certificate-authority=/home/ca.crt  --embed-certs=true --kubeconfig=/home/test.config

      -  If the public API server address is used, run the following command:

         .. code-block::

            kubectl config set-cluster test-arm --server=https://10.0.1.100:5443 --kubeconfig=/home/test.config --insecure-skip-tls-verify=true

      |image5|

   .. note::

      If you **perform operations on a node in the cluster** or **the node that uses the configuration is a cluster node**, do not set the path of kubeconfig to **/root/.kube/config**.

   The cluster API server address is an intranet API server address. After an EIP is bound to the cluster, the cluster API server address can also be a public API server address.

#. Configure the cluster authentication information.

   a. Obtain the cluster token. (If the token is obtained in GET mode, you need to run **based64 -d** to decode the token.)

   .. code-block::

      token=$(kubectl describe secret my-sa-token-5gpl4 -n test | awk '/token:/{print $2}')

   b. Set the cluster user **ui-admin**.

   .. code-block::

      kubectl config set-credentials ui-admin --token=$token --kubeconfig=/home/test.config

   |image6|

#. .. _cce_bestpractice_00221__en-us_topic_0235296162_li147965421277:

   Configure the context information for cluster authentication. **ui-admin@test** is the context name.

   .. code-block::

      kubectl config set-context ui-admin@test --cluster=test-arm --user=ui-admin --kubeconfig=/home/test.config

   |image7|

#. .. _cce_bestpractice_00221__en-us_topic_0235296162_li1088912408273:

   Set the context. For details about how to use the context, see :ref:`Permissions Verification <cce_bestpractice_00221__en-us_topic_0235296162_section14884146153319>`.

   .. code-block::

      kubectl config use-context ui-admin@test --kubeconfig=/home/test.config

   |image8|

   .. note::

      If you want to assign other users the above permissions to perform operations on the cluster, provide the generated configuration file **/home/test.config** to the user after performing step :ref:`6 <cce_bestpractice_00221__en-us_topic_0235296162_li147965421277>`. The user must ensure that the host can access the API server address of the cluster. When performing step :ref:`7 <cce_bestpractice_00221__en-us_topic_0235296162_li1088912408273>` on the host and using kubectl, the user must set the kubeconfig parameter to the path of the configuration file.

.. _cce_bestpractice_00221__en-us_topic_0235296162_section14884146153319:

Permissions Verification
------------------------

#. Pods in the **test** namespace cannot access pods in other namespaces.

   .. code-block::

      kubectl get pod -n test --kubeconfig=/home/test.config

   |image9|

#. Pods in the **test** namespace cannot be deleted.

   |image10|

Further Readings
----------------

For more information about users and identity authentication in Kubernetes, see `Authenticating <https://kubernetes.io/docs/reference/access-authn-authz/authentication/>`__.

.. |image1| image:: /_static/images/en-us_image_0271457115.png
.. |image2| image:: /_static/images/en-us_image_0271467350.png
.. |image3| image:: /_static/images/en-us_image_0271467469.png
.. |image4| image:: /_static/images/en-us_image_0271463079.png
.. |image5| image:: /_static/images/en-us_image_0271466158.png
.. |image6| image:: /_static/images/en-us_image_0271466198.png
.. |image7| image:: /_static/images/en-us_image_0271466336.png
.. |image8| image:: /_static/images/en-us_image_0271466320.png
.. |image9| image:: /_static/images/en-us_image_0271466402.png
.. |image10| image:: /_static/images/en-us_image_0271466430.png
