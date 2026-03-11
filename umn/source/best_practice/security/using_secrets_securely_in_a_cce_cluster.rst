:original_name: cce_bestpractice_0320.html

.. _cce_bestpractice_0320:

Using Secrets Securely in a CCE Cluster
=======================================

CCE now provides static encryption for secrets. Secrets created by users are encrypted and stored in the clusters' etcd. Currently, Secrets are mainly used as environment variables or through file mounts. Regardless of which method is used, CCE always delivers the data exactly as you originally configured it. Therefore, it is advised to:

#. Avoid logging any sensitive information.

#. Configure stricter permissions if secrets are used through file mounts and the default file permission inside the container is 0644. An example is as follows:

   .. code-block::

      apiversion: v1
      kind: Pod
      metadata:
        name: mypod
      spec:
        containers:
        - name: mypod
          image: redis
          volumeMounts:
          - name: foo
            mountPath: "/etc/foo"
        volumes:
        - name: foo
          secret:
            secretName: mysecret
            defaultMode: 256

   In **defaultMode: 256**, **256** is a decimal number, which corresponds to the octal number **0400**.

#. Hide secrets in containers by customizing the file names when mounting them as files.

   .. code-block::

      apiVersion: v1
      kind: Secret
      metadata:
        name: dotfile-secret
      data:
        .secret-file: dmFsdWUtMg0KDQo=
      ---
      apiVersion: v1
      kind: Pod
      metadata:
        name: secret-dotfiles-pod
      spec:
        volumes:
        - name: secret-volume
          secret:
            secretName: dotfile-secret
        containers:
        - name: dotfile-test-container
          image: k8s.gcr.io/busybox
          command:
          - ls
          - "-1"
          - "/etc/secret-volume"
          volumeMounts:
          - name: secret-volume
            readOnly: true
            mountPath: "/etc/secret-volume"

   In this way, **.secret-file** cannot be seen by running **ls -l** in the **/etc/secret-volume/** directory, but can be viewed by running **ls -al**.

#. Encrypt sensitive data by yourself before creating secrets and decrypt them only when needed.

Using a Bound Service Account Token to Access a Cluster
-------------------------------------------------------

Service account tokens based on secrets do not support expiration settings or automatic updates. Because they are stored in secrets, the tokens remain in the secrets even after the pods are deleted. This can pose a security risk if the tokens are leaked. For CCE clusters v1.23 or later, it is advised to use bound service account tokens. They support token expiration settings and align the tokens' lifecycle with the pods, reducing the risk of credential leakage. An example is as follows:

.. code-block::

   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: security-token-example
     namespace: security-example
   spec:
     replicas: 1
     selector:
       matchLabels:
         app: security-token-example
         label: security-token-example
     template:
       metadata:
         annotations:
           seccomp.security.alpha.kubernetes.io/pod: runtime/default
         labels:
           app: security-token-example
           label: security-token-example
       spec:
         serviceAccountName: test-sa
         containers:
           - image: ...
             imagePullPolicy: Always
             name: security-token-example
         volumes:
           - name: test-projected
             projected:
               defaultMode: 420
               sources:
                 - serviceAccountToken:
                     expirationSeconds: 1800
                     path: token
                 - configMap:
                     items:
                       - key: ca.crt
                         path: ca.crt
                     name: kube-root-ca.crt
                 - downwardAPI:
                     items:
                       - fieldRef:
                           apiVersion: v1
                           fieldPath: metadata.namespace
                         path: namespace

For details, see `Managing Service Accounts <https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/>`__.
