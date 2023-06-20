:original_name: cce_10_0388.html

.. _cce_10_0388:

Cluster Secrets
===============

By default, CCE creates the following secrets in each namespace:

-  default-secret
-  paas.elb
-  default-token-*xxxxx* (*xxxxx* is a random number.)

The functions of these secrets are described as follows.

.. _cce_10_0388__section11760122012591:

default-secret
--------------

The type of **default-secret** is **kubernetes.io/dockerconfigjson**. The data is the credential for logging in to the SWR image repository and is used to pull images from SWR. If you need to pull an image from SWR when creating a workload on CCE, set **imagePullSecrets** to **default-secret**.

.. code-block::

   apiVersion: v1
   kind: Pod
   metadata:
     name: nginx
   spec:
     containers:
     - image: nginx:alpine
       name: container-0
       resources:
         limits:
           cpu: 100m
           memory: 200Mi
         requests:
           cpu: 100m
           memory: 200Mi
     imagePullSecrets:
     - name: default-secret

The data of **default-secret** is updated periodically, and the current data will expire after a certain period of time. You can run the **describe** command to view the expiration time in of default-secret.

.. important::

   Use default-secret directly instead of copying the secret content to create a new one. The credential in the copied secret will expire and the image cannot be pulled.

.. code-block::

   $ kubectl describe secret default-secret
   Name:         default-secret
   Namespace:    default
   Labels:       secret-generated-by=cce
   Annotations:  temporary-ak-sk-expires-at: 2021-11-26 20:55:31.380909 +0000 UTC

   Type:  kubernetes.io/dockerconfigjson

   Data
   ====
   .dockerconfigjson:  347 bytes

paas.elb
--------

The data of **paas.elb** is the temporary AK/SK data, which is used to create ELB load balancers during Service and ingress creation. The data of paas.elb is periodically updated and expires after a certain period of time.

In practice, you will not directly use paas.elb. However, do not delete it. Otherwise, ELB load balancers will fail to be created.

default-token-xxxxx
-------------------

By default, Kubernetes creates a service account named **default** for each namespace. **default-token-xxxxx** is the key of the service account, and **xxxxx** is a random number.

.. code-block::

   $ kubectl get sa
   NAME     SECRETS   AGE
   default  1         30d
   $ kubectl describe sa default
   Name:                default
   Namespace:           default
   Labels:              <none>
   Annotations:         <none>
   Image pull secrets:  <none>
   Mountable secrets:   default-token-vssmw
   Tokens:              default-token-vssmw
   Events:              <none>
