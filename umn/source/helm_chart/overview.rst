:original_name: cce_10_0191.html

.. _cce_10_0191:

Overview
========

CCE provides a console for managing Helm charts, helping you easily deploy applications using the charts and manage applications on the console.

Helm
----

`Helm <https://helm.sh/>`__ is a package manager for Kubernetes and manages charts. A Helm chart is a series of YAML files used to encapsulate native Kubernetes applications. When deploying an application, you can customize some metadata of the application for easy application distribution. Application releasors can use Helm to package applications, manage application dependencies and application versions, and release applications to the software repository. After using Helm, users do not need to compile complex application deployment files. They can easily search for, install, upgrade, roll back, and uninstall applications on Kubernetes.

The relationship between Helm and Kubernetes is as follows:

-  Helm <-> Kubernetes
-  Apt <-> Ubuntu
-  Yum <-> CentOS
-  Pip <-> Python

The following figure shows the solution architecture:

|image1|

Helm can help application orchestration for Kubernetes:

-  Manages, edits, and updates a large number of Kubernetes configuration files.
-  Deploys a complex Kubernetes application that contains a large number of configuration files.
-  Shares and reuses Kubernetes configurations and applications.
-  Supports multiple environments with parameter-based configuration templates.
-  Manages the release of applications, including rolling back the application, finding differences (using the **diff** command), and viewing the release history.
-  Controls phases in a deployment cycle.
-  Tests and verifies the released version.

.. |image1| image:: /_static/images/en-us_image_0000001981276109.png
