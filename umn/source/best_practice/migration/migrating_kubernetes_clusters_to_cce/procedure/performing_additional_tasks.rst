:original_name: cce_bestpractice_0313.html

.. _cce_bestpractice_0313:

Performing Additional Tasks
===========================

Verifying Application Functions
-------------------------------

Cluster migration involves full migration of application data, which may cause intra-application adaptation problems. In this example, after the cluster is migrated, the redirection link of the article published in WordPress is still the original domain name. If you click the article title, you will be redirected to the application in the source cluster. Therefore, search for the original domain name in WordPress and replace it with the new domain name, change the values of **site_url** and primary URL in the database. For details, see `Changing The Site URL <https://wordpress.org/support/article/changing-the-site-url/>`__.

Access the new address of the WordPress application. If the article published before the migration is displayed, the data of the persistent volume is successfully restored.

|image1|

Switching Live Traffic to the Target Cluster
--------------------------------------------

O&M personnel switch DNS to direct live traffic to the target cluster.

-  DNS traffic switching: Adjust the DNS configuration to switch traffic.
-  Client traffic switching: Upgrade the client code or update the configuration to switch traffic.

Bringing the Source Cluster Offline
-----------------------------------

After confirming that the service on the target cluster is normal, bring the source cluster offline and delete the backup files.

-  Verify that the service on the target cluster is running properly.
-  Bring the source cluster offline.
-  Delete backup files.

.. |image1| image:: /_static/images/en-us_image_0000001897905113.png
