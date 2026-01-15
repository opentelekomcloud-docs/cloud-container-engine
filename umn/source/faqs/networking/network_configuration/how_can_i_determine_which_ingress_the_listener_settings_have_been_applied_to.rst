:original_name: cce_faq_00460.html

.. _cce_faq_00460:

How Can I Determine Which Ingress the Listener Settings Have Been Applied To?
=============================================================================

With CCE, you can associate multiple ingresses with a single load balancer listener and establish various forwarding policies. Listener configuration parameters are stored in annotations, which means that a listener can have different configuration parameters on different ingresses. This section describes how to determine which ingress the listener settings are applied to. It covers:

.. _cce_faq_00460__section175820196816:

Determining Which Ingress the Listener Settings Have Been Applied To
--------------------------------------------------------------------

Listener parameters can be configured for all ingresses associated with the same listener, so CCE uses the listener annotation configurations (excluding SNI certificates) from the earliest created ingress (the first ingress). The first ingress is determined by sorting the **metadata.createTimestamp** fields of the ingresses in ascending order.

-  The first ingress information is written into annotations in clusters v1.21.15-r0, v1.23.14-r0, v1.25.9-r0, v1.27.6-r0, v1.28.4-r0, v1.29.1-r0, and later. You can check **kubernetes.io/elb.listener-master-ingress** in the annotations of the existing ingresses.

   |image1|

-  To get the first ingress in clusters earlier than v1.29.1-r0, v1.28.4-r0, v1.27.6-r0, v1.25.9-r0, v1.23.14-r0, and v1.21.15-r0, use the kubectl command to obtain the ingresses associated with the same load balancer listener, sort these ingresses in ascending order based on their creation time, and check the first one.

   The query command is as follows: (Replace the load balancer ID and port number as needed.)

   .. code-block::

      elb_id=${1}
      elb_port=${2}
      kubectl get ingress --all-namespaces --sort-by='.metadata.creationTimestamp' -o=custom-columns=Name:'metadata.name',Namespace:'metadata.namespace',elbID:'metadata.annotations.kubernetes\.io\/elb\.id',elbPort:'metadata.annotations.kubernetes\.io\/elb\.port',elbPorts:'metadata.annotations.kubernetes\.io\/elb\.listen-ports' | awk 'NR==1 {print; next} {if ($5 != "<none>") $4 = "<none>"; print}' | grep -E "^Name|${elb_id}" | grep -E "^Name|${elb_port}" | awk '{printf "%-30s %-30s %-38s %-10s %-10s\n", $1, $2, $3, $4, $5}'

   In the command output, the first column specifies the ingress names, the second specifies the namespaces, the third specifies the load balancer IDs, the fourth specifies the listener ports, and the fifth specifies the ports of multiple listeners. (If multiple listener port numbers are configured, they will replace the listener port numbers and become effective.)

   .. code-block::

      Name             Namespace   elbID                                  elbPort   elbPorts
      ingress-first    default     be56202a-c2cb-40d5-900e-d7a007a4b054   443       <none>
      ingress-second   default     be56202a-c2cb-40d5-900e-d7a007a4b054   443       <none>
      ingress-third    test        be56202a-c2cb-40d5-900e-d7a007a4b054   443       <none>

Configuring and Updating a Listener Certificate
-----------------------------------------------

You can configure an ingress certificate in a cluster using either of the following methods:

-  Use a TLS certificate, keep it as a secret, and manage it on CCE. TLS certificates are configured using the **spec.tls** fields in ingresses. They will be automatically created, updated, or deleted via the ELB console.

-  Use a certificate created in the ELB service. The certificate content is maintained on the ELB console. Such certificates are configured in the **annotation** fields in ingresses.

   ELB server certificates are also maintained on the ELB console, so you do not need to import the certificate content to CCE secrets. This allows for unified cross-namespace configuration. It is recommended that you use ELB server certificates to configure the certificates for ingresses.

   ELB server certificates are supported in clusters v1.19.16-r2, v1.21.5-r0, and v1.23.3-r0.

To update the listener server certificate created by an ingress using a TLS certificate, follow these steps:

#. Check the command in :ref:`Determining Which Ingress the Listener Settings Have Been Applied To <cce_faq_00460__section175820196816>` and obtain all ingresses associated with the same listener.

   The command output is as follows:

   .. code-block::

      Name             Namespace   elbID                                  elbPort   elbPorts
      ingress-first    default     be56202a-c2cb-40d5-900e-d7a007a4b054   443       <none>
      ingress-second   default     be56202a-c2cb-40d5-900e-d7a007a4b054   443       <none>
      ingress-third    test        be56202a-c2cb-40d5-900e-d7a007a4b054   443       <none>

#. Obtain the certificate configuration in the first ingress.

   .. code-block::

      apiVersion: networking.k8s.io/v1
      kind: Ingress
      metadata:
        name: ingress-first
        namespace: default
        ...
      spec:
        tls:
          - secretName: default-ns-secret-1
          - hosts:
              - 'example.com'
            secretName: default-ns-secret-2
      ...

#. Update the configurations of **default-ns-secret-1** and **default-ns-secret-2** in the first ingress.

#. After the key is updated, log in to the network console. In the navigation pane, choose **Elastic Load Balance** > **Certificates**. In the right pane, check whether the server certificate has been updated based on the update time.

To update the listener server certificate created by an ingress using an ELB certificate, follow these steps:

#. On the ELB console, obtain the ID of the server certificate used by the load balancer listener.

   a. Log in to the network console. In the navigation pane, choose **Elastic Load Balance** > **Load Balancers**. In the right pane, click the name of the target load balancer.
   b. Click the **Listeners** tab and click the name of the target listener to go to the details page.
   c. On the **Summary** tab, obtain the server certificate ID.

#. In the navigation pane, choose **Elastic Load Balance** > **Certificates**, search for the certificate based on the obtained server certificate ID, locate the row containing the target certificate, and click **Modify** in the **Operation** column.

Impacts of Deleting the First Ingress on Listener Settings
----------------------------------------------------------

Listener settings only apply to the first ingress configuration (excluding SNI certificates). If the first ingress is deleted, the earliest created ingress in use becomes the new first ingress, and the listener settings will be updated accordingly. This means that if the listener settings of the old and new first ingresses are different, there may be unexpected updates on the ELB console. To avoid this, check if the listener configuration of the ingress that will become the new first ingress is the same as the one for the original first ingress, or otherwise meets your expectations.

-  You can synchronize the listener settings on the console. The procedure is as follows:

   #. Log in to the CCE console and click the cluster name to access the cluster console.
   #. In the navigation pane, choose **Services**, click the **Ingresses** tab, and choose **More** > **Update** in the **Operation** column.
   #. Click **Synchronize** to automatically synchronize the server certificate. This option is available when the listener settings of the ingress are inconsistent with those of the load balancer.

      .. note::

         If you synchronize a server certificate and an SNI certificate, and the current ingress is using a **TLS key**, the certificate will be replaced with an ELB server certificate. If the cluster version is earlier than v1.19.16-r2, v1.21.5-r0, v1.23.3-r0 and does not support ELB server certificates, you need to :ref:`manually synchronize the configurations using YAML <cce_faq_00460__li617913510232>`.

   #. Click **OK** to apply the modification.

-  .. _cce_faq_00460__li617913510232:

   You can manually synchronize the listener settings using YAML. The procedure is as follows:

   #. Check the command in :ref:`Determining Which Ingress the Listener Settings Have Been Applied To <cce_faq_00460__section175820196816>` and obtain all ingresses associated with the same listener.

      The command output is as follows:

      .. code-block::

         Name             Namespace   elbID                                  elbPort   elbPorts
         ingress-first    default     be56202a-c2cb-40d5-900e-d7a007a4b054   443       <none>
         ingress-second   default     be56202a-c2cb-40d5-900e-d7a007a4b054   443       <none>
         ingress-third    test        be56202a-c2cb-40d5-900e-d7a007a4b054   443       <none>

   #. Before deleting some ingresses such as **ingress-first** and **ingress-second**, synchronize the listener settings of **ingress-first** to the annotations of **ingress-third**.

      If the listener server certificate was created using a TLS key, you need to synchronize the configurations saved in the ingress' **spec.tls** to **ingress-third**.

      .. code-block::

         apiVersion: networking.k8s.io/v1
         kind: Ingress
         metadata:
           name: ingress-first
           namespace: default
           ...
         spec:
           tls:
             - secretName: default-ns-secret-1
             - hosts:
                 - 'example.com'
               secretName: default-ns-secret-2
         ...

.. |image1| image:: /_static/images/en-us_image_0000002484117972.png
