:original_name: cce_bulletin_0133.html

.. _cce_bulletin_0133:

CCE Agency Changes
===================

Background
------------

To improve the security of CCE agencies and provide more refined permission control, CCE has adjusted and upgraded the dependent agencies as follows:
- **Agency permission isolation and least privilege**: Added an independent agency to provide temporary credentials for user node system components in CCE clusters.

Cluster Operations May Be Affected
-------------------------------------------

New agencies are automatically generated when a cluster is created or upgraded to v1.28.15-r80, v1.29.15-r40, v1.30.14-r40, v1.31.14-r0, v1.32.9-r0, v1.33.7-r0, or a later version, for the first time. If the required agencies already exist, no further action is taken. Ensure that your account has the IAM permissions listed below, otherwise, agency provisioning may fail and impact cluster deployment or upgrade operations.

.. note::

    Based on the principle of least privilege (PoLP), an IAM user must have the following IAM operation permissions when creating or updating an agency:

    - **iam:agencies:createAgency**: for creating an agency
    - **iam:permissions:revokeRoleFromAgencyOnProject**: for removing permissions of an agency for a region-specific project
    - **iam:permissions:grantRoleToAgencyOnProject**: for granting permissions to an agency for a region-specific project
    - **iam:permissions:grantRoleToAgency**: for granting permissions to an agency
    - **iam:roles:createRole**: for creating a custom policy
    - **iam:roles:updateRole**: for modifying a custom policy
    - **iam:roles:listRoles**: for listing permissions
    - **iam:agencies:listAgencies**: for querying agencies based on specified conditions

.. note::

    For details about the new agencies, see Adjustment: `Agency Permission Isolation and Least Privilege <cce_bulletin_0133__section214823415110>`.
    For more information, see :ref:`Creating a Custom Policy <iam_01_0016>`, :ref:`Assigning Permissions to an IAM User <en-us_topic_0079496985>` and :ref:`Managing Permissions of a User Group <iam_03_0004>`.

Detailed Description of Agency Adjustment
--------------------------------------------

.. _cce_bulletin_0133__section214823415110:

Added CCENodeAgency.
^^^^^^^^^^^^^^^^^^^^^^

Providing temporary access credentials, such as authentication details for accessing SWR image repositories from components running on CCE worker nodes.

.. table:: **Agencies before adjustment**

    +-------------------------------------------------------------------------------+-------------------+-----------------------------------------------+
    | Scenario                                                                      | Old Agency        | Applicable Version                            |
    +===============================================================================+===================+===============================================+
    | - Providing temporary access credentials for CCE, including the minimum       | cce_admin_trust   | Supported in all versions                     |
    |   permissions required by CCE to manage clusters and node lifecycles          |                   |                                               |
    | - Providing temporary access credentials for components on the CCE cluster    |                   | For details, see :ref:`System Agencies        |
    |   control plane                                                               |                   | <cce_10_0556>`.                               |
    | - Providing temporary access credentials for various CCE cluster add-ons      |                   |                                               |
    | - Providing credentials for CCE clusters to access SWR image repositories     |                   |                                               |
    | - Providing temporary access credentials for components running on the worker |                   |                                               |
    |   nodes in CCE clusters                                                       |                   |                                               |
    +-------------------------------------------------------------------------------+-------------------+-----------------------------------------------+

.. table:: **Agencies after adjustment**

    +-------------------------------------------------------------------------------+-------------------+-----------------------------------------------+
    | Scenario                                                                      | New Agency        | Applicable Version                            |
    +===============================================================================+===================+===============================================+
    | - Providing temporary access credentials for CCE, including the minimum       | cce_admin_trust   | Supported in all versions                     |
    |   permissions required by CCE to manage clusters and node lifecycles          |                   |                                               |
    | - Providing temporary access credentials for components on the CCE cluster    |                   | For details, see :ref:`System Agencies        |
    |   control plane                                                               |                   | <cce_10_0556>`.                               |
    | - Providing temporary access credentials for various CCE cluster add-ons      |                   |                                               |
    +-------------------------------------------------------------------------------+-------------------+-----------------------------------------------+
    | - Providing credentials for CCE clusters to access SWR image repositories     | CCENodeAgency     | The cluster version is v1.28.15-r80,          |
    | - Providing temporary access credentials for components running on the worker |                   | v1.29.15-r40, v1.30.14-r40, v1.31.14-r0,      |
    |   nodes in CCE clusters                                                       |                   | v1.32.9-r0, v1.33.7-r0 or later.              |
    |                                                                               |                   |                                               |
    |                                                                               |                   | For details, see :ref:`System Agencies        |
    |                                                                               |                   | <cce_10_0556>`.                               |
    +-------------------------------------------------------------------------------+-------------------+-----------------------------------------------+
                                                     