:original_name: cce_10_0508.html

.. _cce_10_0508:

NGINX Ingress Controller
========================

Check Items
-----------

-  Check item 1: Check whether there is an Nginx Ingress route whose ingress type is not specified (**kubernetes.io/ingress.class: nginx** is not added to **annotations**) in the cluster.
-  Check item 2: Check whether the DefaultBackend Service specified by the NGINX Ingress Controller backend is available.

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

   In the preceding command, ``cceaddon-nginx-ingress--controller-***`` is the controller pod name, *<controller-name>* is the controller name specified during add-on installation, and *<namespace>* is the namespace where the controller is deployed.

   Command output:

   .. code-block::

      - '--default-backend-service=<namespace>/<backend-svc-name>'

   In the preceding command, *<backend-svc-name>* is the name of the DefaultBackend Service for the NGINX Ingress Controller.

#. Check whether the DefaultBackend Service of the NGINX Ingress Controller is available.

   .. code-block::

      kubectl get svc <backend-svc-name> -n <namespace>

   If the Service is unavailable, this check item failed.

Solution
--------

**For Check Item 1**

Add an annotation to the Nginx ingresses as follows:

.. code-block::

   kubectl annotate ingress <ingress-name> kubernetes.io/ingress.class=nginx

.. important::

   There is no need to add this annotation to LoadBalancer ingresses. :ref:`Verify <cce_10_0508__section18775201773016>` that these ingresses are managed by NGINX Ingress Controller.

**For Check Item 2**

Create the DefaultBackend Service again.

-  If a custom DefaultBackend Service has been specified in the default 404 service configuration during add-on installation, create the same Service.

-  If the default DefaultBackend Service is used during add-on installation, the re-created YAML example is as follows:

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

.. |image1| image:: /_static/images/en-us_image_0000002218659254.png
.. |image2| image:: /_static/images/en-us_image_0000002218819094.png
.. |image3| image:: /_static/images/en-us_image_0000002253778885.png
