:original_name: cce_10_0508.html

.. _cce_10_0508:

NGINX Ingress Controller
========================

Check Items
-----------

-  Check whether there is an Nginx Ingress route whose ingress type is not specified (**kubernetes.io/ingress.class: nginx** is not added to **annotations**) in the cluster.
-  Check whether the default backend Service specified by the NGINX Ingress Controller backend is available.
-  Check whether the key length of the default server certificate specified by the NGINX Ingress Controller meets the security requirements. (If the length fails to meet the security requirements, error message "xxx" is displayed.)

.. _cce_10_0508__section18775201773016:

Fault Locating
--------------

**For Check Item 1**

For Nginx Ingress, check the YAML. If the ingress type is not specified in the YAML file and the ingress is managed by the NGINX Ingress Controller, the ingress is at risk.

#. Check the ingress type.

   Run the following command:

   .. code-block::

      kubectl get ingress <ingress-name> -oyaml | grep -E ' kubernetes.io/ingress.class: | ingressClassName:' -B 1

   -  Fault scenario: If the command output is empty, the ingress type is not specified.

   -  Normal scenario: The command output is not empty, indicating that the ingress type has been specified by **annotations** or **ingressClassName**.

      |image1|

#. Ensure that the ingress is managed by the Nginx ingress Controller. The LoadBalancer Ingresses are not affected by this issue.

   -  For clusters of v1.19, confirm this issue using **managedFields**.

      .. code-block::

         kubectl get ingress <ingress-name> -oyaml | grep 'manager: nginx-ingress-controller'

      |image2|

   -  For clusters of other versions, check the logs of the NGINX Ingress Controller pod.

      .. code-block::

          kubectl logs -nkube-system cceaddon-nginx-ingress-controller-545db6b4f7-bv74t | grep 'updating Ingress status'

      |image3|

   If the fault persists, contact technical support.

**For Check Item 2**

#. View the DefaultBackend Service in the namespace where the NGINX Ingress Controller is deployed.

   .. code-block::

      kubectl get pod cceaddon-nginx-ingress-<controller-name>-controller-*** -n <namespace> -oyaml | grep 'default-backend'

   ``cceaddon-nginx-ingress--controller-***`` is the controller pod name, *<controller-name>* is the controller name specified during add-on installation, and *<namespace>* is the namespace where the controller is deployed.

   Command output:

   .. code-block::

      - '--default-backend-service=<namespace>/<backend-svc-name>'

   *<backend-svc-name>* is the name of the DefaultBackend Service for the NGINX Ingress Controller.

#. Check whether the default backend Service of the NGINX Ingress Controller is available.

   .. code-block::

      kubectl get svc <backend-svc-name> -n <namespace>

   If the Service is unavailable, this check item failed.

**For Check Item 3**

#. .. _cce_10_0508__li7636134164413:

   Check whether there is a default server certificate in the namespace where the NGINX Ingress Controller is deployed.

   .. code-block::

      kubectl get pod cceaddon-nginx-ingress-<controller-name>-controller-*** -n <namespace> -oyaml | grep 'default-ssl-certificate'

   ``cceaddon-nginx-ingress--controller-***`` is the controller pod name, *<controller-name>* is the controller name specified during add-on installation, and *<namespace>* is the namespace where the controller is deployed.

   Command output:

   .. code-block::

      - '--default-ssl-certificate=<namespace>/<secret-name>'

   *<secret-name>* indicates the secret name for the default server certificate specified by the NGINX Ingress Controller.

#. Check whether the default certificate used by the NGINX Ingress Controller has a security risk due to insufficient encryption length.

   .. code-block::

      kubectl get secret <secret-name> -n <namespace> -o=custom-columns=:'data.tls\.key' | base64 -d | openssl <encryption-algorithm> -noout -text

   *<encryption-algorithm>* is the encryption algorithm used by the certificate. Common encryption algorithms include RSA and DSA.

   Method of obtaining an RSA key length

   |image4|

   Method of obtaining a DSA key length

   |image5|

Solution
--------

**For Check Item 1**

Add an annotation to the Nginx ingresses as follows:

.. code-block::

   kubectl annotate ingress <ingress-name> kubernetes.io/ingress.class=nginx

.. important::

   There is no need to add this annotation to LoadBalancer ingresses. :ref:`Verify <cce_10_0508__section18775201773016>` that these ingresses are managed by NGINX Ingress Controller.

**For Check Item 2**

Create the default backend Service again.

-  If a custom default backend Service has been specified in the default 404 service configuration during add-on installation, create the same Service.

-  If the default backend Service is used during add-on installation, the following is an example YAML file for re-creating a Service:

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        name: cceaddon-nginx-ingress-<controller-name>-default-backend   # <controller-name> is the controller name.
        namespace: kube-system
        labels:
          app: nginx-ingress-<controller-name>
          app.kubernetes.io/managed-by: Helm
          chart: nginx-ingress-<version>    # <version> is the add-on version.
          component: default-backend
          heritage: Helm
          release: cceaddon-nginx-ingress-<controller-name>
        annotations:
          meta.helm.sh/release-name: cceaddon-nginx-ingress-<controller-name>
          meta.helm.sh/release-namespace: kube-system    # Namespace where the add-on is installed
      spec:
        ports:
          - name: http
            protocol: TCP
            port: 80
            targetPort: http
        selector:
          app: nginx-ingress-<controller-name>
          component: default-backend
          release: cceaddon-nginx-ingress-<controller-name>
        type: ClusterIP
        sessionAffinity: None
        ipFamilies:
          - IPv4
        ipFamilyPolicy: SingleStack
        internalTrafficPolicy: Cluster

**For Check Item 3**

-  Change the secret of the certificate described in :ref:`1 <cce_10_0508__li7636134164413>` and configure a certificate that meets the key length specified by OpenSSL SECLEVEL. For details about the relationship between the security levels and key lengths, see `official OpenSSL documents <https://docs.openssl.org/3.3/man3/SSL_CTX_set_security_level/#default-callback-behaviour>`__.
-  Add the **@SECLEVEL** field to the **ssl-ciphers** parameter to support certificates with lower security levels.

.. |image1| image:: /_static/images/en-us_image_0000002483958008.png
.. |image2| image:: /_static/images/en-us_image_0000002483958012.png
.. |image3| image:: /_static/images/en-us_image_0000002484117990.png
.. |image4| image:: /_static/images/en-us_image_0000002483959286.png
.. |image5| image:: /_static/images/en-us_image_0000002484119256.png
