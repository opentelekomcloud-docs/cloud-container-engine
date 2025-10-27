:original_name: cce_10_0930.html

.. _cce_10_0930:

Updating the HTTPS Certificate for a LoadBalancer Ingress
=========================================================

If the HTTPS certificate for a LoadBalancer ingress is about to expire or has expired, follow the operations provided in this section to update it.

Application Scenarios
---------------------

+-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Scenario                          | Description                                                                                                                                                                                                                                                               |
+===================================+===========================================================================================================================================================================================================================================================================+
| LoadBalancer Service certificate  | To update an HTTPS certificate, create a LoadBalancer Service certificate and select it when modifying the ingress.                                                                                                                                                       |
|                                   |                                                                                                                                                                                                                                                                           |
|                                   | Alternatively, modify the certificate on the ELB certificate management page.                                                                                                                                                                                             |
+-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| TLS certificate                   | To update an HTTPS certificate, update the target secret in the cluster. CCE will then automatically configure the certificate (starting with **k8s_plb_default**) on the ELB. The certificate automatically created by CCE **cannot be modified or deleted on the ELB**. |
+-----------------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

Procedure for TLS Secret Certificates
-------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose ConfigMaps and Secrets. In the right pane, click the **Secrets** tab, find the secret used by the ingress, and click **Update**.
#. Modify the certificate file in the secret and click **OK** to update the secret. CCE will automatically synchronize the certificate to the ELB.

Procedure for LoadBalancer Service Certificates
-----------------------------------------------

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. In the navigation pane, choose **Services & Ingresses**. Then, click the **Ingresses** tab, locate the row containing the target ingress, and choose **More** > **Update** in the **Operation** column.
#. Set **Certificate Source** to **ELB server certificate**, select a new server certificate, and click **OK** to update the ingress.
