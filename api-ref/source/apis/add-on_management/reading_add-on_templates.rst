:original_name: cce_02_0321.html

.. _cce_02_0321:

Reading Add-on Templates
========================

Function
--------

This API is used to query add-on information.

.. note::

   The URL for add-on management is in the format of **https://{clusterid}.Endpoint/uri.** In the URL, *{clusterid}* indicates the cluster ID, and *uri* indicates the resource path, that is, the path for API access.

URI
---

GET /api/v3/addontemplates

:ref:`Table 1 <cce_02_0321__table737034819716>` describes the parameters of this API.

.. _cce_02_0321__table737034819716:

.. table:: **Table 1** Parameter description

   +-----------------------+-----------------------+---------------------------------------------------------------------------------------------+
   | Parameter             | Mandatory             | Description                                                                                 |
   +=======================+=======================+=============================================================================================+
   | addon_template_name   | No                    | Name of the specified template. If this parameter is left blank, all templates are queried. |
   |                       |                       |                                                                                             |
   |                       |                       | Minimum: **2**                                                                              |
   |                       |                       |                                                                                             |
   |                       |                       | Maximum: **30**                                                                             |
   +-----------------------+-----------------------+---------------------------------------------------------------------------------------------+

.. _cce_02_0321__section93858481877:

Request
-------

**Request parameters:**

:ref:`Table 2 <cce_02_0321__table8687245142017>` lists the request parameters.

.. _cce_02_0321__table8687245142017:

.. table:: **Table 2** Parameters in the request header

   +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | Parameter             | Mandatory             | Description                                                                                                                                                                                                                                                                   |
   +=======================+=======================+===============================================================================================================================================================================================================================================================================+
   | Content-Type          | Yes                   | Message body type (format).                                                                                                                                                                                                                                                   |
   |                       |                       |                                                                                                                                                                                                                                                                               |
   |                       |                       | Default: **application/json**                                                                                                                                                                                                                                                 |
   +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
   | X-Auth-Token          | Yes                   | Requests for calling an API can be authenticated using either a token or AK/SK. If token-based authentication is used, this parameter is mandatory and must be set to a user token. For details on how to obtain a user token, see :ref:`API Usage Guidelines <cce_02_0344>`. |
   |                       |                       |                                                                                                                                                                                                                                                                               |
   |                       |                       | Maximum: **16384**                                                                                                                                                                                                                                                            |
   +-----------------------+-----------------------+-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

**Example request:**

NA

Response
--------

For details about the response parameters, see :ref:`Table 3 <cce_02_0321__responseparameter>`.

.. _cce_02_0321__responseparameter:

.. table:: **Table 3** Parameters in the response body

   +------------+------------------------------------------------------------+------------------------------------------------------------------+
   | Parameter  | Type                                                       | Description                                                      |
   +============+============================================================+==================================================================+
   | kind       | String                                                     | API type. The value is fixed at **Addon** and cannot be changed. |
   +------------+------------------------------------------------------------+------------------------------------------------------------------+
   | apiVersion | String                                                     | API version. The value is fixed at **v3** and cannot be changed. |
   +------------+------------------------------------------------------------+------------------------------------------------------------------+
   | items      | :ref:`items <cce_02_0321__response_addontemplate>` objects | Add-on template list.                                            |
   +------------+------------------------------------------------------------+------------------------------------------------------------------+

.. _cce_02_0321__response_addontemplate:

.. table:: **Table 4** Data structure of the items field

   +------------+---------------------------------------------------------+----------------------------------------------------------------------------+
   | Parameter  | Type                                                    | Description                                                                |
   +============+=========================================================+============================================================================+
   | kind       | String                                                  | API type. The value is fixed at **Addon** and cannot be changed.           |
   +------------+---------------------------------------------------------+----------------------------------------------------------------------------+
   | apiVersion | String                                                  | API version. The value is fixed at **v3** and cannot be changed.           |
   +------------+---------------------------------------------------------+----------------------------------------------------------------------------+
   | metadata   | :ref:`metadata <cce_02_0321__response_metadata>` object | Basic information about an add-on. Metadata is a collection of attributes. |
   +------------+---------------------------------------------------------+----------------------------------------------------------------------------+
   | spec       | :ref:`spec <cce_02_0321__response_templatespec>` object | Detailed description of the add-on template.                               |
   +------------+---------------------------------------------------------+----------------------------------------------------------------------------+

.. _cce_02_0321__response_metadata:

.. table:: **Table 5** Data structure of the metadata field

   +-------------------+--------+------------------------------------------------------+
   | Parameter         | Type   | Description                                          |
   +===================+========+======================================================+
   | uid               | String | Unique ID of the add-on template.                    |
   +-------------------+--------+------------------------------------------------------+
   | name              | String | Add-on name.                                         |
   +-------------------+--------+------------------------------------------------------+
   | labels            | Object | Add-on labels in the format of key-value pairs.      |
   +-------------------+--------+------------------------------------------------------+
   | annotations       | Object | Add-on annotations in the format of key-value pairs. |
   +-------------------+--------+------------------------------------------------------+
   | updateTimestamp   | String | Time when the add-on instance was updated.           |
   +-------------------+--------+------------------------------------------------------+
   | creationTimestamp | String | Time when the add-on instance was created            |
   +-------------------+--------+------------------------------------------------------+

.. _cce_02_0321__response_templatespec:

.. table:: **Table 6** Data structure of the spec field

   +-------------+-------------------------------------------------------------------+---------------------------------------------+
   | Parameter   | Type                                                              | Description                                 |
   +=============+===================================================================+=============================================+
   | type        | String                                                            | Template type (helm or static).             |
   +-------------+-------------------------------------------------------------------+---------------------------------------------+
   | require     | Boolean                                                           | Whether the add-on is installed by default. |
   +-------------+-------------------------------------------------------------------+---------------------------------------------+
   | labels      | Array of strings                                                  | Group to which the template belongs.        |
   +-------------+-------------------------------------------------------------------+---------------------------------------------+
   | logoURL     | String                                                            | URL of the logo image.                      |
   +-------------+-------------------------------------------------------------------+---------------------------------------------+
   | readmeURL   | String                                                            | URL of the readme file.                     |
   +-------------+-------------------------------------------------------------------+---------------------------------------------+
   | description | String                                                            | Template description.                       |
   +-------------+-------------------------------------------------------------------+---------------------------------------------+
   | versions    | Array of :ref:`versions <cce_02_0321__response_versions>` objects | Template version details.                   |
   +-------------+-------------------------------------------------------------------+---------------------------------------------+

.. _cce_02_0321__response_versions:

.. table:: **Table 7** Data structure of the versions field

   +-------------------+---------------------------------------------------------------------------------+----------------------------------------------------+
   | Parameter         | Type                                                                            | Description                                        |
   +===================+=================================================================================+====================================================+
   | version           | String                                                                          | Add-on version.                                    |
   +-------------------+---------------------------------------------------------------------------------+----------------------------------------------------+
   | input             | Object                                                                          | Add-on installation parameters.                    |
   +-------------------+---------------------------------------------------------------------------------+----------------------------------------------------+
   | stable            | Boolean                                                                         | Whether the add-on version is a stable release.    |
   +-------------------+---------------------------------------------------------------------------------+----------------------------------------------------+
   | translate         | Object                                                                          | Translation information used by the GUI.           |
   +-------------------+---------------------------------------------------------------------------------+----------------------------------------------------+
   | supportVersions   | Array of :ref:`supportVersions <cce_02_0321__response_supportversions>` objects | Cluster versions that support the add-on template. |
   +-------------------+---------------------------------------------------------------------------------+----------------------------------------------------+
   | creationTimestamp | String                                                                          | Creation time of the add-on instance.              |
   +-------------------+---------------------------------------------------------------------------------+----------------------------------------------------+
   | updateTimestamp   | String                                                                          | Time when the add-on instance was updated.         |
   +-------------------+---------------------------------------------------------------------------------+----------------------------------------------------+

.. _cce_02_0321__response_supportversions:

.. table:: **Table 8** Data structure of the supportVersions field

   +----------------+------------------+-------------------------------------------------------------------------------------------------+
   | Parameter      | Type             | Description                                                                                     |
   +================+==================+=================================================================================================+
   | clusterType    | String           | Cluster type that supports the add-on template.                                                 |
   +----------------+------------------+-------------------------------------------------------------------------------------------------+
   | clusterVersion | Array of strings | Cluster versions that support the add-on template. The parameter value is a regular expression. |
   +----------------+------------------+-------------------------------------------------------------------------------------------------+

**Example response:**

.. code-block::

   {
       "kind": "Addon",
       "apiVersion": "v3",
       "items": [
           {
               "kind": "Addon",
               "apiVersion": "v3",
               "metadata": {
                   "uid": "coredns",
                   "name": "coredns",
                   "creationTimestamp": "2020-07-13T20:04:33Z",
                   "updateTimestamp": "2020-07-13T20:04:34Z"
               },
               "spec": {
                   "type": "helm",
                   "require": true,
                   "labels": [
                       "ServiceDiscovery"
                   ],
                   "logoURL": "https://***/cce-addon-aw1hz2u/corednslogo.svg",
                   "description": "CoreDNS is a DNS server that chains plugins and provides Kubernetes DNS Services",
                   "versions": [
                       {
                           "version": "1.15.3",
                           "input": {
                               "basic": {
                                   "cluster_ip": "10.247.3.10",
                                   "platform": "linux-amd64",
                                   "swr_addr": "10.125.13.11:20202",
                                   "swr_user": "test"
                               },
                               "parameters": {
                                   "custom": {
                                       "stub_domains": "",
                                       "upstream_nameservers": ""
                                   },
                                   "flavor1": {
                                       "name": 2500,
                                       "replicas": 2,
                                       "resources": [
                                           {
                                               "limitsCpu": "500m",
                                               "limitsMem": "512Mi",
                                               "name": "coredns",
                                               "requestsCpu": "500m",
                                               "requestsMem": "512Mi"
                                           }
                                       ]
                                   },
                                   "flavor2": {
                                       "name": 5000,
                                       "replicas": 2,
                                       "resources": [
                                           {
                                               "limitsCpu": "1000m",
                                               "limitsMem": "1024Mi",
                                               "name": "coredns",
                                               "requestsCpu": "1000m",
                                               "requestsMem": "1024Mi"
                                           }
                                       ]
                                   },
                                   "flavor3": {
                                       "name": 10000,
                                       "replicas": 2,
                                       "resources": [
                                           {
                                               "limitsCpu": "2000m",
                                               "limitsMem": "2048Mi",
                                               "name": "coredns",
                                               "requestsCpu": "2000m",
                                               "requestsMem": "2048Mi"
                                           }
                                       ]
                                   },
                                   "flavor4": {
                                       "name": 20000,
                                       "replicas": 4,
                                       "resources": [
                                           {
                                               "limitsCpu": "2000m",
                                               "limitsMem": "2048Mi",
                                               "name": "coredns",
                                               "requestsCpu": "2000m",
                                               "requestsMem": "2048Mi"
                                           }
                                       ]
                                   }
                               }
                           },
                           "stable": true,
                           "translate": {
                               "en_US": {
                                   "addon": {
                                       "changeLog": "add workaround for klog/coredns crash issue",
                                       "description": "CoreDNS is a DNS server that chains plugins and provides Kubernetes DNS Services"
                                   },
                                   "description": {
                                       "Parameters.custom.stub_domains": "The target nameserver may itself be a Kubernetes service. For instance, you can run your own copy of dnsmasq to export custom DNS names into the ClusterDNS namespace, a JSON map using a DNS suffix key (e.g. "acme.local") and a value consisting of a JSON array of DNS IPs.",
                                       "Parameters.custom.upstream_nameservers": "If specified, then the values specified replace the nameservers taken by default from the node's /etc/resolv.conf. Limits:a maximum of three upstream nameservers can be specified, A JSON array of DNS IPs.",
                                       "Parameters.flavor1.description": "Concurrent domain name resolution ability  -  External domain name:2500 qps,  Internal domain name:10000 qps",
                                       "Parameters.flavor1.name": 2500,
                                       "Parameters.flavor2.description": "Concurrent domain name resolution ability  -  External domain name:5000 qps, Internal domain name:20000 qps",
                                       "Parameters.flavor2.name": 5000,
                                       "Parameters.flavor3.description": "Concurrent domain name resolution ability  -  External domain name:10000 qps, Internal domain name:40000 qps",
                                       "Parameters.flavor3.name": 10000,
                                       "Parameters.flavor4.description": "Concurrent domain name resolution ability  -  External domain name:20000 qps, Internal domain name:80000 qps",
                                       "Parameters.flavor4.name": 20000
                                   },
                                   "key": {
                                       "Parameters.custom.stub_domains": "stub domain",
                                       "Parameters.custom.upstream_nameservers": "upstream nameservers"
                                   }
                               },
                               "fr_FR": {
                                   "addon": {
                                       "changeLog": "ajouter une solution de contournement pour le problème de plantage klog / coredns",
                                       "description": "Un serveur DNS qui enchaîne les plug-ins et fournit des services DNS Kubernetes."
                                   },
                                   "description": {
                                       "Parameters.custom.stub_domains": "Le serveur de noms cible peut lui-même être un service Kubernetes. Par exemple, vous pouvez exécuter votre propre copie de dnsmasq pour exporter des noms DNS personnalisés dans l'espace de noms ClusterDNS, une carte JSON à l'aide d'une clé de suffixe DNS (par exemple, «acme.local») et une valeur constituée d'un tableau JSON d'adresses IP DNS.",
                                       "Parameters.custom.upstream_nameservers": "Si spécifié, les valeurs spécifiées remplacent les serveurs de noms pris par défaut dans le fichier /etc/resolv.conf du nœud. Limites: un maximum de trois serveurs de noms en amont peuvent être spécifiés, un tableau JSON d'adresses IP DNS.",
                                       "Parameters.flavor1.description": "Capacité de résolution de nom de domaine simultanée - Nom de domaine externe: 2500 qps, Nom de domaine interne: 10000 qp",
                                       "Parameters.flavor1.name": 2500,
                                       "Parameters.flavor2.description": "Capacité de résolution de nom de domaine simultanée - Nom de domaine externe: 5000 qps, Nom de domaine interne: 20000 qp",
                                       "Parameters.flavor2.name": 5000,
                                       "Parameters.flavor3.description": "Capacité de résolution de nom de domaine simultanée - Nom de domaine externe: 10000 qps, Nom de domaine interne: 40000 qp",
                                       "Parameters.flavor3.name": 10000,
                                       "Parameters.flavor4.description": "Capacité de résolution de nom de domaine simultanée - Nom de domaine externe: 20000 qps, Nom de domaine interne: 80000 qp",
                                       "Parameters.flavor4.name": 20000
                                   },
                                   "key": {
                                       "Parameters.custom.stub_domains": "domaine stub",
                                       "Parameters.custom.upstream_nameservers": "serveurs de noms en amont"
                                   }
                               },
                               "zh_CN": {
                                   "addon": {
                                       "changeLog": "",
                                       "description": ""
                                   },
                                   "description": {
                                       "Parameters.custom.stub_domains": "",
                                       "Parameters.custom.upstream_nameservers": "",
                                       "Parameters.flavor1.description": "",
                                       "Parameters.flavor1.name": 2500,
                                       "Parameters.flavor2.description": "",
                                       "Parameters.flavor2.name": 5000,
                                       "Parameters.flavor3.description": "",
                                       "Parameters.flavor3.name": 10000,
                                       "Parameters.flavor4.description": "",
                                       "Parameters.flavor4.name": 20000
                                   },
                                   "key": {
                                       "Parameters.custom.stub_domains": "",
                                       "Parameters.custom.upstream_nameservers": ""
                                   }
                               }
                           },
                           "supportVersions": [
                               {
                                   "clusterType": "VirtualMachine",
                                   "clusterVersion": [
                                       "v1.15.*"
                                   ]
                               }
                           ],
                           "creationTimestamp": "2020-07-13T20:04:33Z",
                           "updateTimestamp": "2020-07-13T20:04:33Z"
                       }
                   ]
               }
           }
       ]
   }

Status Codes
------------

:ref:`Table 9 <cce_02_0321__en-us_topic_0079614900_table46761928>` describes the status codes of this API.

.. _cce_02_0321__en-us_topic_0079614900_table46761928:

.. table:: **Table 9** Status codes

   =========== ===========
   Status Code Description
   =========== ===========
   200         OK
   =========== ===========

For the description about error status codes, see :ref:`Status Code <cce_02_0084>`.
