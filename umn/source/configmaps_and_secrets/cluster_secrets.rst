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

The type of **default-secret** is **kubernetes.io/dockerconfigjson**. The data is the credential for logging in to the SWR image repository and is used to pull images from SWR. To pull an image from SWR when creating a workload on CCE, set **imagePullSecrets** to **default-secret**.

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

The data of **default-secret** is updated periodically, and the current data will expire after a certain period of time. You can run the **describe** command to view the expiration time in default-secret.

.. important::

   Use default-secret directly instead of copying the secret content to create a new one. The credential in the copied secret will expire and the image cannot be pulled.

.. code-block::

   kubectl describe secret default-secret

Command output:

.. code-block::

   Name:         default-secret
   Namespace:    default
   Labels:       secret-generated-by=cce
   Annotations:  swr-auth-may-expires-at: 2021-11-26 20:55:31.380909 +0000 UTC

   Type:  kubernetes.io/dockerconfigjson

   Data
   ====
   .dockerconfigjson:  347 bytes

paas.elb
--------

The **paas.elb** data stores a temporary AK/SK that is used when a node is created or a load balancer is automatically created. The **paas.elb** data is updated periodically and has a specific time limit before it expires.

In practice, you will not directly use **paas.elb**. Do not delete it, as doing so will result in the failure of creating a node or load balancer.

default-token-xxxxx
-------------------

By default, Kubernetes creates a service account named **default** for each namespace. **default-token-xxxxx** is the key of the service account, and **xxxxx** is a random number.

.. note::

   In clusters v1.25 or later, a secret is not created automatically for each ServiceAccount. For details, see :ref:`Service Account Token Security Improvement <cce_10_0477>`.

#. Check the service account in the cluster.

   .. code-block::

      kubectl get sa

   Command output:

   .. code-block::

      NAME     SECRETS   AGE
      default  1         30d

#. Run the following command to view the secret:

   .. code-block::

      kubectl describe sa default

   Command output:

   .. code-block::

      Name:                default
      Namespace:           default
      Labels:              <none>
      Annotations:         <none>
      Image pull secrets:  <none>
      Mountable secrets:   default-token-xxxxx
      Tokens:              default-token-xxxxx
      Events:              <none>
