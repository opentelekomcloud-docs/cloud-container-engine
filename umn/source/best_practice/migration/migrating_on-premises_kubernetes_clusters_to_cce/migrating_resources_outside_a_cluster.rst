:original_name: cce_bestpractice_0309.html

.. _cce_bestpractice_0309:

Migrating Resources Outside a Cluster
=====================================

If your migration does not involve resources outside a cluster listed in :ref:`Table 1 <cce_bestpractice_0307__table1126932541820>` or you do not need to use other services to update resources after the migration, skip this section.

Migrating Container Images
--------------------------

To ensure that container images can be properly pulled after cluster migration and improve container deployment efficiency, you are advised to migrate private images to SoftWare Repository for Container (SWR). CCE works with SWR to provide a pipeline for automated container delivery. Images are pulled in parallel, which greatly improves container delivery efficiency.

You need to manually migrate container images.

#. Remotely log in to any node in the source cluster and run the **docker pull** command to pull all images to the local host.

#. Log in to the SWR console, click **Login Command** in the upper right corner of the page, and copy the command.

#. Run the copied login command on the node.

   The message "Login Succeeded" will be displayed upon a successful login.

#. Add tags to all local images.

   .. code-block::

      docker tag [Image name 1:tag 1] [Image repository address]/[Organization name]/[Image name 2:tag 2]

   -  *[Image name 1*:*tag 1]*: name and tag of the local image to be pulled.
   -  *[Image repository address]*: You can query the image repository address on the SWR console.
   -  *[Organization name]*: Enter the name of the organization you created on the SWR console.
   -  *[Image name 2*:*tag 2]*: image name and tag displayed on the SWR console.

   **Example**

   .. code-block::

      docker tag nginx:v1 swr.eu-de.otc.t-systems.com/cloud-develop/mynginx:v1

#. Run the **docker push** command to upload all local container image files to SWR.

   .. code-block::

      docker push [Image repository address]/[Organization name]/[Image name 2:tag 2]

   **Example**

   .. code-block::

      docker push swr.eu-de.otc.t-systems.com/cloud-develop/mynginx:v1

Migrating Databases and Storage (On-Demand)
-------------------------------------------

You can determine whether to use **Relational Database Service (RDS)** and **Object Storage Service (OBS)** based on your production requirements. After the migration is complete, you need to reconfigure the database and storage for applications in the target CCE cluster.
