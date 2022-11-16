:original_name: cce_02_0341.html

.. _cce_02_0341:

Obtaining a Project ID
======================

Obtaining the Project ID by Calling an API
------------------------------------------

You can obtain the project ID by calling the API `used to query project information based on the specified criteria <https://docs.otc.t-systems.com/en-us/api/iam/en-us_topic_0057845625.html>`__.

The API used to obtain a project ID is **GET https://**\ *{Endpoint}*\ **/v3/projects**, where *{Endpoint}* indicates the IAM endpoint. You can obtain the IAM endpoint from `Regions and Endpoints <https://docs.otc.t-systems.com/en-us/endpoint/index.html>`__.

The following is an example response. The value of **id** in the **projects** field is the project ID.

.. code-block::

   {
       "projects": [
           {
               "domain_id": "65382450e8f64ac0870cd180d14e684b",
               "is_domain": false,
               "parent_id": "65382450e8f64ac0870cd180d14e684b",
               "name": "xxxxxxxx",
               "description": "",
               "links": {
                   "next": null,
                   "previous": null,
                   "self": "https://www.example.com/v3/projects/a4a5d4098fb4474fa22cd05f897d6b99"
               },
               "id": "a4a5d4098fb4474fa22cd05f897d6b99",
               "enabled": true
           }
       ],
       "links": {
           "next": null,
           "previous": null,
           "self": "https://www.example.com/v3/projects"
       }
   }

Obtaining a Project ID from the Console
---------------------------------------

A project ID is required for some URLs when an API is called. To obtain a project ID, perform the following operations:

#. Log in to the management console.

#. Click the username and choose **My Credentials** from the drop-down list.

   On the **My Credentials** page, view project IDs in the project list.


   .. figure:: /_static/images/en-us_image_0203982368.png
      :alt: **Figure 1** Obtaining the Project ID

      **Figure 1** Obtaining the Project ID
