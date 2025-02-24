:original_name: cce_10_0744.html

.. _cce_10_0744:

Revoking a Cluster Access Credential
====================================

In multi-tenant scenarios, CCE generates a credential (kubeconfig or X.509 certificate) for you to access the corresponding cluster. The credential contains user identity and authorization information so that you can access the cluster and perform authorized operations on it. Credentials ensure security between IAM users for user management and authorization. However, the validity period of a credential is usually fixed. When an employee holding the credential resigns or an authorized credential is disclosed, you need to manually revoke the credential to ensure cluster security.

The credentials can be revoked in the following scenarios:

-  IAM users (non-administrators) intend to revoke their own credentials.
-  Accounts or administrators (users in the admin user group) can revoke the credentials of other users or delegated accounts.

.. important::

   After a credential is revoked, the holder of the credential cannot access the cluster, and the revoked credential cannot be restored. Exercise caution when performing this operation.

   To access the same cluster, you need to regenerate a credential.

Prerequisites
-------------

A cluster of v1.19.16-r50, v1.21.11-r10, v1.23.9-r10, v1.25.4-r10, v1.27.1-r10, or later is available.

Operations Performed by IAM Users
---------------------------------

IAM users can only revoke their own credentials. To revoke a credential, perform the following operations:

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Choose **Overview** in the navigation pane. In the right pane, locate the **Connection Information** area and click **Revoke** for **Certificate Authentication**.
#. In the dialog box displayed, enter **REVOKE** in the confirmation box and click **OK** if you are sure you want to revoke the credential.

Operations Performed by Accounts or Administrators
--------------------------------------------------

Accounts or administrators (users in the admin user group) can revoke the credentials of other users or delegated accounts. To revoke a credential, see the following operations:

#. Log in to the CCE console and click the cluster name to access the cluster console.
#. Choose **Overview** in the navigation pane. In the right pane, locate the **Connection Information** area and click **Revoke** for **Certificate Authentication**.
#. Select a certificate applicant and revoke the certificate. The revoked certificate cannot be restored. Exercise caution when performing this operation.

   -  **User**: Select an IAM user and revoke its credential.

   -  **Account**: Select an agency account and revoke its credentials.

   -  **Specify User ID/Delegacy ID**: If the user has been deleted or you log in to the system through an identity provider, manually enter the user ID. If the delegated account has been deleted, manually enter the delegated ID.

      You can use the following solution to obtain the ID:

      -  If you can obtain the certificate downloaded by the applicant, the name (**CN - Common Name**) of the certificate is the required ID.
      -  If you cannot obtain the certificate downloaded by the applicant, use Cloud Trace Service (CTS) to obtain the events of deleting a user (**deleteUser**) and deleting an agency (**deleteAgency**). The resource IDs of the events are the IDs of the deleted user and delegated account, respectively.

      If the required ID cannot be obtained using the preceding methods, submit a service ticket to O&M personnel.

#. In the dialog box displayed, enter **REVOKE** in the confirmation box and click **OK** if you are sure you want to revoke the credential.
