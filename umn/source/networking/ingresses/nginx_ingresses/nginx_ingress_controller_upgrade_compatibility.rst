:original_name: cce_10_0968.html

.. _cce_10_0968:

NGINX Ingress Controller Upgrade Compatibility
==============================================

Compatibility
-------------

CCE NGINX Ingress Controller is based on the community version of `Ingress NGINX Controller <https://github.com/kubernetes/ingress-nginx>`__. Upgrades to the community version may introduce new features, optimize existing ones, or address security issues. As a result, compatibility differences may occur after you upgrade CCE NGINX Ingress Controller. These differences can include configuration changes between versions, deprecated Kubernetes APIs, default actions, and compatibility with dependency components.

Before upgrading the NGINX Ingress Controller, pay attention to the following compatibility issues identified by CCE:

-  :ref:`Native Nginx Validity Check Disabled by Default <cce_10_0968__section6945154553715>`
-  :ref:`Snippet Annotations Disabled by Default <cce_10_0968__section11701729145314>`
-  :ref:`Earlier TLS Versions Not Supported <cce_10_0968__section02315551454>`
-  :ref:`Nginx Native root and alias Directives Not Supported <cce_10_0968__section8367415152019>`
-  :ref:`Graceful Upgrades Supported by NGINX Ingress Controller <cce_10_0968__section810118942211>`

.. _cce_10_0968__section6945154553715:

Native Nginx Validity Check Disabled by Default
-----------------------------------------------

**Affected versions: 3.0.34 and later**

.. caution::

   After the upgrade, the default behavior of the add-on will be adjusted. Check whether the change affects your services.

The NGINX Ingress Controller v3.0.34 corresponds to community version `v1.11.5 <https://github.com/kubernetes/ingress-nginx/releases/tag/controller-v1.11.5>`__. In this community version, the security vulnerability **CVE-2025-1974** has been fixed, and *nginx -t* (syntax check) has been removed from webhooks. In the community version `v1.11.5 <https://github.com/kubernetes/ingress-nginx/releases/tag/controller-v1.11.5>`__, the format of ingress resources is still verified using admission webhooks. However, if you enable snippet annotations and the configuration contains syntax errors, invalid configurations may be directly injected into the **nginx.conf** file. Since there is no pre-check to validate the injected configuration, such errors may cause the Nginx configuration reload to fail. Therefore, if you enable snippet annotations, check NGINX Ingress Controller's pod logs for errors each time you modify the ingress rules. To do so, run the following command:

.. code-block::

   kubectl logs -f {nginx-ingress-controller-pod-name} -n kube-system | grep Error

.. _cce_10_0968__section11701729145314:

Snippet Annotations Disabled by Default
---------------------------------------

**Affected versions: 2.4.6 and later**

.. caution::

   After the add-on is upgraded, the existing incompatible settings are not updated. These settings do not take effect by default in the new version. You need to perform additional configurations.

Starting from version 2.4.6, the NGINX Ingress Controller has disabled snippet annotations by default to improve security and configuration stability. The snippet annotations include:

-  nginx.ingress.kubernetes.io/configuration-snippet
-  nginx.ingress.kubernetes.io/server-snippet
-  nginx.ingress.kubernetes.io/stream-snippet
-  nginx.ingress.kubernetes.io/auth-snippet
-  nginx.ingress.kubernetes.io/modsecurity-snippet

If you still need to use snippet annotations, fully evaluate the associated risks and manually enable the annotation function. To do so, go to **Settings**, click **YAML** under **Nginx Parameters** and add the following annotations:

-  *"allow-snippet-annotations": "true"*
-  *"annotations-risk-level": "Critical"*

   .. note::

      In NGINX Ingress Controller 4.0.4 and later versions, you need to add **annotations-risk-level**.

      This is because the default value of **annotations-risk-level** is downgraded to **High** in these versions. For details, see `Changelog <https://github.com/kubernetes/ingress-nginx/releases/tag/controller-v1.12.0>`__. Snippet annotations are at the critical level. So, you need to change the value of **annotations-risk-level** to **Critical**, or snippet annotations are still unavailable.

.. _cce_10_0968__section02315551454:

Earlier TLS Versions Not Supported
----------------------------------

**Affected versions: 2.3.3 and later**

.. caution::

   After the upgrade, the default behavior of the add-on will be adjusted. Check whether the change affects your services.

TLS v1.1 and earlier versions have security issues. The NGINX Ingress Controller of v2.3.3 and later versions does not support TLS v1.1 and TLS v1.0 by default. Therefore, before upgrading the NGINX Ingress Controller, ensure your services do not rely on TLS v1.1 or earlier versions. Remove these versions from your configuration to maintain compatibility and security.

.. _cce_10_0968__section8367415152019:

Nginx Native root and alias Directives Not Supported
----------------------------------------------------

**Affected versions: 2.1.1 and later**

.. caution::

   After the add-on is upgraded, the existing incompatible settings are not updated. These settings do not take effect in the new version.

To prevent sensitive file leakage or path conflicts caused by incorrect configurations, the NGINX Ingress Controller of v2.1.1 and later versions has removed support for the *root* and *alias* directives. Therefore, before upgrading the NGINX Ingress Controller, ensure your ingresses do not contain the Nginx native *root* or *alias* directives configured using snippets.

.. _cce_10_0968__section810118942211:

Graceful Upgrades Supported by NGINX Ingress Controller
-------------------------------------------------------

To ensure a smooth upgrade and maintain service stability, pay attention to the following add-on versions:

-  **2.1.x**: The NGINX Ingress Controller of v2.1.33 and later versions supports graceful shutdown and hitless upgrade.
-  **2.2.x**: The NGINX Ingress Controller of v2.2.42 and later versions supports graceful shutdown and hitless upgrade.
-  **2.4.6 or later**: The NGINX Ingress Controller supports graceful shutdown and hitless upgrade.

For versions beyond the preceding ranges, services may experience temporary downtime during upgrades. To ensure service continuity and stability, plan and conduct an upgrade during **off-peak hours**.
