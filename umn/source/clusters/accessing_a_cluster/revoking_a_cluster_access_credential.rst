:original_name: cce_10_0744.html

.. _cce_10_0744:

Revoking a Cluster Access Credential
====================================

In multi-tenant scenarios, CCE generates a unique credential (such as a kubeconfig file or an X.509 certificate) for each user to access their designated cluster. These credentials contain user identity and authorization details, enabling users to perform authorized operations while ensuring secure isolation and management. However, credentials typically have a fixed validity period. If an employee resigns or a credential is compromised, manual revocation is required to maintain cluster security.

The credentials can be revoked in the following scenarios:

-  IAM users (non-administrators) intend to revoke their own credentials.
-  Accounts or administrators (users in the admin user group) can revoke the credentials of other users or delegated accounts.

.. important::

   After a credential is revoked, the holder of the credential cannot access the cluster, and the revoked credential cannot be restored. Exercise caution when performing this operation.

   To access the same cluster, you need to regenerate a credential.

Prerequisites
-------------

A cluster v1.19.16-r50, v1.21.11-r10, v1.23.9-r10, v1.25.4-r10, v1.27.1-r10, or later is available.

Operations Performed by IAM Users
---------------------------------

IAM users can only revoke their own credentials. To revoke a credential, perform the following operations:

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Choose **Overview** in the navigation pane. On the **Overview** page, locate the **Connection Information** area on the right, click **Revoke**.
#. In the dialog box displayed, enter **REVOKE** in the confirmation box and click **OK** if you are sure you want to revoke the credential.

Operations Performed by Accounts or Administrators
--------------------------------------------------

Accounts or administrators (users in the admin user group) can revoke the credentials of other users or delegated accounts. To revoke a credential, perform the following operations:

#. Log in to the CCE console and click the cluster name to access the cluster console.

#. Choose **Overview** in the navigation pane. On the **Overview** page, locate the **Connection Information** area on the right, click **Revoke**.

#. Select a certificate applicant to revoke the certificate. For details about the parameters, see :ref:`Table 1 <cce_10_0744__table1450610254177>`. After the certificate is revoked, the original X.509 certificate and kubectl configuration file become invalid. If the certificate applicant needs to regain access to the cluster, they must download the certificate or kubectl configuration file again.

   .. _cce_10_0744__table1450610254177:

   .. table:: **Table 1** Certificate applicant

      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                         | Description                                                                                                                                                                                                                                                |
      +===================================+============================================================================================================================================================================================================================================================+
      | User                              | Select an IAM user and revoke their credential.                                                                                                                                                                                                            |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Account                           | Select an agency account and revoke their credential.                                                                                                                                                                                                      |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Other user/agency ID              | If your IAM user or account has been deleted, or if you log in to the system through an identity provider, you need to manually enter the user ID or agency ID. To obtain an ID, do as follows:                                                            |
      |                                   |                                                                                                                                                                                                                                                            |
      |                                   | -  If you can obtain the certificate downloaded by the applicant, the certificate's common name (CN) serves as the required ID.                                                                                                                            |
      |                                   | -  If you cannot obtain the certificate downloaded by the applicant, use Cloud Trace Service (CTS) to retrieve the **deleteUser** and **deleteAgency** events. The resource IDs of these events are the IDs of the deleted user and account, respectively. |
      |                                   |                                                                                                                                                                                                                                                            |
      |                                   | If the required ID cannot be obtained using the preceding methods, submit a service ticket to O&M personnel.                                                                                                                                               |
      +-----------------------------------+------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

#. In the dialog box displayed, enter **REVOKE** in the confirmation box and click **OK** if you are sure you want to revoke the credential.
