:original_name: cce_bestpractice_0320.html

.. _cce_bestpractice_0320:

Secret Security
===============

Currently, CCE has configured static encryption for secret resources. The secrets created by users will be encrypted and stored in etcd of the CCE cluster. Secrets can be used in two modes: environment variable and file mounting. No matter which mode is used, CCE still transfers the configured data to users. Therefore, it is recommended that:

#. Do not record sensitive information in logs.

#. For the secret that uses the file mounting mode, the default file permission mapped in the container is 0644. Configure stricter permissions for the file. For example:

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

#. When the file mounting mode is used, configure the secret file name to hide the file in the container.

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

   In this way, **.secret-file** cannot be viewed by running the **ls -l** command in the **/etc/secret-volume/** directory, but can be viewed by running the **ls -al** command.

#. Encrypt sensitive information before creating a secret and decrypt the information when using it.

Using a Bound ServiceAccount Token to Access a Cluster
------------------------------------------------------

The secret-based ServiceAccount token does not support expiration time or auto update. In addition, after the mounting pod is deleted, the token is still stored in the secret. Token leakage may incur security risks. A bound ServiceAccount token is recommended for CCE clusters of version 1.23 or later. In this mode, the expiration time can be set and is the same as the pod lifecycle, reducing token leakage risks. Example:

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

For details, visit https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/.
