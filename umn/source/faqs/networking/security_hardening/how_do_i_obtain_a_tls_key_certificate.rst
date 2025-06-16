:original_name: cce_faq_00185.html

.. _cce_faq_00185:

How Do I Obtain a TLS Key Certificate?
======================================

Scenario
--------

If your ingress needs to use HTTPS, you must configure a secret of the IngressTLS or kubernetes.io/tls type when creating an ingress.

When creating a secret, ensure that the certificate file uploaded in the secret data must match the private key file. Otherwise, the certificate file becomes invalid.

Solution
--------

Generally, you need to obtain a valid certificate from the certificate provider. If you want to use it in the test environment, you can create a certificate and private key by the performing the following steps.

.. note::

   Self-created certificates apply only to test scenarios. Such certificates are invalid and will affect browser access. Manually upload a valid one to ensure secure connections.

#. Generate a tls.key.

   .. code-block::

      openssl genrsa -out tls.key 2048

   The command will generate a private tls.key in the directory where the command is executed.

#. Generate a certificate using the private tls.key.

   .. code-block::

      openssl req -new -x509 -key tls.key -out tls.crt -subj /C=CN/ST=******/O=Devops/CN=example.com -days 3650

   The generated key must be in the following format:

   .. code-block::

      ----BEGIN RSA PRIVATE KEY-----
      ...........................................................
      -----END RSA PRIVATE KEY-----

   The generated certificate must be in the following format:

   .. code-block::

      -----BEGIN CERTIFICATE-----
      ................................................................
      -----END CERTIFICATE-----

#. Import the certificate.

   When creating a TLS secret, import the certificate and private key file to the corresponding location.

Verification
------------

The ingress address can be accessed through a browser. However, the certificate and secret are not issued by the CA, so the CA does not recognize them and shows a message saying they are insecure.
