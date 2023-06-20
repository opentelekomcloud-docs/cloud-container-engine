:original_name: cce_10_0477.html

.. _cce_10_0477:

Service Account Token Security Improvement
==========================================

In clusters earlier than v1.21, a token is obtained by mounting the secret of the service account to a pod. Tokens obtained this way are permanent. This approach is no longer recommended starting from version 1.21. Service accounts will stop auto creating secrets in clusters from version 1.25.

In clusters of version 1.21 or later, you can use the `TokenRequest <https://kubernetes.io/docs/reference/kubernetes-api/authentication-resources/token-request-v1/>`__ API to obtain the token and use the projected volume to mount the token to the pod. Such tokens are valid for a fixed period (one hour by default). Before expiration, Kubelet refreshes the token to ensure that the pod always uses a valid token. When the mounting pod is deleted, the token automatically becomes invalid. This approach is implemented by the `BoundServiceAccountTokenVolume <https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/#bound-service-account-token-volume>`__ feature to improve the token security of the service account. Kubernetes clusters of v1.21 and later enables this approach by default.

For smooth transition, the community extends the token validity period to one year by default. After one year, the token becomes invalid, and clients that do not support certificate reloading cannot access the API server. It is recommended that clients of earlier versions be upgraded as soon as possible. Otherwise, service faults may occur.

If you use a Kubernetes client of a to-be-outdated version, the certificate reloading may fail. Versions of officially supported Kubernetes client libraries able to reload tokens are as follows:

-  Go: >= v0.15.7
-  Python: >= v12.0.0
-  Java: >= v9.0.0
-  Javascript: >= v0.10.3
-  Ruby: master branch
-  Haskell: v0.3.0.0
-  C#: >= 7.0.5

For details, visit https://github.com/kubernetes/enhancements/tree/master/keps/sig-auth/1205-bound-service-account-tokens.

.. note::

   If you need a token that never expires, you can also `manually manage secrets for service accounts <https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/#manual-secret-management-for-serviceaccounts>`__. Although a permanent service account token can be manually created, you are advised to use a short-lived token by calling the `TokenRequest <https://kubernetes.io/docs/reference/kubernetes-api/authentication-resources/token-request-v1/>`__ API for higher security.

Diagnosis
---------

Run the following steps to check your CCE clusters of v1.21 and later:

#. Use kubectl to connect to the cluster and run the **kubectl get --raw "/metrics" \| grep stale** command to query the metrics. Check the metric named **serviceaccount_stale_tokens_total**.

   If the value is greater than 0, some workloads in the cluster may be using an earlier client-go version. In this case, check whether this problem occurs in your deployed applications. If yes, upgrade client-go to the version specified by the community as soon as possible. The version must be at least two major versions of the CCE cluster. For example, if your cluster version is 1.23, the Kubernetes dependency library version must be at least 1.19.

   |image1|

.. |image1| image:: /_static/images/en-us_image_0000001518062816.png
