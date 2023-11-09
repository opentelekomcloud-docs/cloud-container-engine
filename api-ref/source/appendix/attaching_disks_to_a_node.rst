:original_name: node_storage_example.html

.. _node_storage_example:

Attaching Disks to a Node
=========================

Challenges
----------

In disk planning and striped logical disk creation, it is difficult for users to flexibly attach and partition disks when creating a node.

During node creation, the **storage** field selects a data disk based on parameters, such as the disk size and disk type, to prevent failures in node creation, resetting, migration, and management caused by drive letter matching failures.

Solution
--------

This section provides details about the **storage** field used in node creation so that you can implement complex disk selection and partitioning by calling the node creation API.

The **storage** field consists of **storageSelectors** and **storageGroups**. The **storageSelectors** field is responsible for disk selection, and the **storageGroups** field is responsible for disk processing.

The basic logic for field matching is as follows:

|image1|

#. **storageSelectors** selects an EVS disk or a local disk based on the value of **storageType**.

   a. Local disks do not support exact match. All local disks will be selected as data disks.

      If some local disks need to be reserved, occupy the disks in the pre-installation script. The script is similar to the following:

      .. code-block::

         # prepare
         vgName=vg-test
         storageDevice=/dev/vdb
         # vgcreate
         vgcreate ${vgName} ${storageDevice}

   b. EVS disks match the disks created in **dataVolumes** based on the settings of **matchLabels**.

#. Policies have priorities to match **matchLabels**. The **matchLabels** policy nearest to **storageSelectors** has a higher priority and the disk nearest to **dataVolumes** will be preferentially matches. As **matchLabels** uses loose matching, you are advised to place the **matchLabels** policy with a small matching range on the top. For example:

   |image2|

   a. In step 1, the first disk in **dataVolumes** is matched by the EVS disk whose size is 100 GiB and storage class is SAS. In step 2, the second disk in **dataVolumes** is matched by the EVS disk whose size is 100 GiB because the first disk has been selected.
   b. In step 3, two disks in **dataVolumes** can be matched because **volumeType** or **count** is not specified in **matchLabels**. In this case, no disk is available for matching in step 4.

#. **storageGroups** associates with **storageSelectors** based on **selectorName**. Finally, two 100 GiB disks are selected. The CCE backend groups the two PVs into a volume group (VG) and divides the VG into two logical volumes (LVs) in the ratio of 9:1. 10% of Kubernetes LVs are partitioned in striped mode. 90% runtime LVs are partitioned in linear mode by default because runtimeConfig is not configured.

Creating a Raw Disk
-------------------

During node creation on the CCE console, click **Add Data Disk**. Then, click **Expand** next to the newly added data disk and select **Default**. The created disk is a raw disk.

The following figure shows the API calling logic.

|image3|

#. The **cceUse** selector matches a 100 GiB data disk.
#. The selected disk is managed by CCE and used as a data disk.
#. The other 100 GiB data disk created in **dataVolumes** is not selected by any selector and is managed by storageGroups. Therefore, this EVS disk will be attached to the node as a raw disk and will not be initialized.

After the node is created, log in to the node and check whether a 100 GiB disk has been attached but not initialized.

|image4|

The following is an API example:

.. code-block::

   {
       "kind": "Node",
       "apiVersion": "v3",
       "metadata": {
           "name": "test-83790"
       },
       "spec": {
           "flavor": "c3.large.2",
           "az": "eu-de-01",
           "os": "EulerOS 2.9",
           "dataVolumes": [
               {
                   "size": 100,
                   "volumetype": "SAS"
               },
               {
                   "size": 100,
                   "volumetype": "SAS"
               }
           ],
           "billingMode": 0,
           "extendParam": {
               "maxPods": 110
           },
           "nodeNicSpec": {
               "primaryNic": {
                   "subnetId": "ca964acf-8468-4735-8229-97940ef6c881"
               }
           },
           "rootVolume": {
               "size": 50,
               "volumetype": "SAS"
           },
           "runtime": {
               "name": "docker"
           },
           "login": {
               "userPassword": {
                   "username": "root",
                   "password": "******"
               }
           },
           "storage": {
               "storageSelectors": [
                   {
                       "name": "cceUse",
                       "storageType": "evs",
                       "matchLabels": {
                           "size": "100",
                           "volumeType": "SAS",
                           "count": "1"
                       }
                   }
               ],
               "storageGroups": [
                   {
                       "name": "vgpaas",
                       "selectorNames": [
                           "cceUse"
                       ],
                       "cceManaged": true,
                       "virtualSpaces": [
                           {
                               "name": "runtime",
                               "size": "90%"
                           },
                           {
                               "name": "kubernetes",
                               "size": "10%"
                           }
                       ]
                   }
               ]
           },
           "count": 1
       }
   }

Attaching a Disk to a Specified Path
------------------------------------

During node creation on the CCE console, click **Add Data Disk**. Then, click **Expand** next to the newly added data disk, select **Mount Disk**, and set the mount path. In this case, CCE initializes and attaches the disk by default.

The following figure shows the API calling logic.

|image5|

#. The **user1** selector selects a 100 GiB data disk.
#. Create a VG named **vguser1** using LVM.
#. Strip all the space of **vguser1** into an LV named **user** and format the disk in ext4 format. Finally, attach the disk to the **/tmp2** directory.

After the node is created, log in to the node and check whether a 100 GiB disk has been attached and managed by LVM.

|image6|

The following is an API example. There are two data disks. One is used by CCE, and the other is mounted to the **/tmp2** directory.

.. code-block::

   {
       "kind": "Node",
       "apiVersion": "v3",
       "metadata": {
           "name": "test-37106"
       },
       "spec": {
           "flavor": "c3.large.2",
           "az": "eu-de-01",
           "os": "EulerOS 2.9",
           "dataVolumes": [
               {
                   "size": 100,
                   "volumetype": "SAS"
               },
               {
                   "size": 100,
                   "volumetype": "SAS"
               }
           ],
           "billingMode": 0,
           "extendParam": {
               "maxPods": 110
           },
           "nodeNicSpec": {
               "primaryNic": {
                   "subnetId": "ca964acf-8468-4735-8229-97940ef6c881"
               }
           },
           "rootVolume": {
               "size": 50,
               "volumetype": "SAS"
           },
           "runtime": {
               "name": "docker"
           },
           "login": {
               "userPassword": {
                   "username": "root",
                   "password": "******"
               }
           },
           "storage": {
               "storageSelectors": [
                   {
                       "name": "cceUse",
                       "storageType": "evs",
                       "matchLabels": {
                           "size": "100",
                           "volumeType": "SAS",
                           "count": "1"
                       }
                   },
                   {
                       "name": "user1",
                       "storageType": "evs",
                       "matchLabels": {
                           "size": "100",
                           "volumeType": "SAS",
                           "count": "1"
                       }
                   }
               ],
               "storageGroups": [
                   {
                       "name": "vgpaas",
                       "selectorNames": [
                           "cceUse"
                       ],
                       "cceManaged": true,
                       "virtualSpaces": [
                           {
                               "name": "runtime",
                               "size": "80%"
                           },
                           {
                               "name": "kubernetes",
                               "size": "20%"
                           }
                       ]
                   },
                   {
                       "name": "vguser1",
                       "selectorNames": [
                           "user1"
                       ],
                       "virtualSpaces": [
                           {
                               "name": "user",
                               "size": "100%",
                               "lvmConfig": {
                                   "lvType": "linear",
                                   "path": "/tmp2"
                               }
                           }
                       ]
                   }
               ]
           },
           "count": 1
       }
   }

Creating Striped LVs to Improve Disk Performance
------------------------------------------------

Currently, the striped LV function is supported only by calling an API. The following is an example:

|image7|

#. **storageSelectors** matches all EVS disks in **dataVolumes** because **matchLabels** is not contained in **storageSelectors**.
#. Create a VG named **vgpaas** using LVM.
#. Strip 90% of the **vgpaas** space into runtime LVs.
#. Strip 10% of the **vgpaas** space into Kubernetes LVs.

.. note::

   -  Two or more data disks are required for striping.
   -  When creating a striped LV, ensure that the types and sizes of the PVs added to the VG are the same. Otherwise, the creation will fail.
   -  When creating a striped LV, use the striping configuration for both the runtime LV and Kubernetes LV. Otherwise, the creation will fail.

Log in to the node and run the following command to view the striping result:

|image8|

The following is an API example:

.. code-block::

   {
       "kind": "Node",
       "apiVersion": "v3",
       "metadata": {
           "name": "test-83773"
       },
       "spec": {
           "flavor": "c3.large.2",
           "az": "eu-de-01",
           "os": "EulerOS 2.9",
           "dataVolumes": [
               {
                   "size": 100,
                   "volumetype": "SAS"
               },
               {
                   "size": 100,
                   "volumetype": "SAS"
               }
           ],
           "billingMode": 0,
           "extendParam": {
               "maxPods": 110
           },
           "nodeNicSpec": {
               "primaryNic": {
                   "subnetId": "ca964acf-8468-4735-8229-97940ef6c881"
               }
           },
           "rootVolume": {
               "size": 50,
               "volumetype": "SAS"
           },
           "runtime": {
               "name": "docker"
           },
           "login": {
               "userPassword": {
                   "username": "root",
                   "password": "******"
               }
           },
           "storage": {
               "storageSelectors": [
                   {
                       "name": "cceUse",
                       "storageType": "evs"
                   }
               ],
               "storageGroups": [
                   {
                       "name": "vgpaas",
                       "selectorNames": [
                           "cceUse"
                       ],
                       "cceManaged": true,
                       "virtualSpaces": [
                           {
                               "name": "runtime",
                               "size": "90%",
                               "runtimeConfig": {
                                   "lvType": "striped"
                               }
                           },
                           {
                               "name": "kubernetes",
                               "size": "10%",
                               "lvmConfig": {
                                   "lvType": "striped"
                               }
                           }
                       ]
                   }
               ]
           },
           "count": 1
       }
   }

.. |image1| image:: /_static/images/en-us_image_0000001207457192.png
.. |image2| image:: /_static/images/en-us_image_0000001207617538.png
.. |image3| image:: /_static/images/en-us_image_0000001252018205.png
.. |image4| image:: /_static/images/en-us_image_0000001207129076.png
.. |image5| image:: /_static/images/en-us_image_0000001207618830.png
.. |image6| image:: /_static/images/en-us_image_0000001207129902.png
.. |image7| image:: /_static/images/en-us_image_0000001252020583.png
.. |image8| image:: /_static/images/en-us_image_0000001251962373.png
