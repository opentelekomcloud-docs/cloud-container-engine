:original_name: cce_bestpractice_00228.html

.. _cce_bestpractice_00228:

Initializing an Application Using an Init Container
===================================================

Concepts
--------

An init container starts and exits before the main containers start. If there are multiple init containers, they will be started in the defined sequence. The data generated in the init containers can be used by the main containers because storage volumes in a pod are shared.

Init containers can be used in multiple Kubernetes resources, such as Deployments, DaemonSets, and jobs. They perform initialization before main containers are started.

Application Scenarios
---------------------

Before deploying a service, you can use an init container to make preparations before the service pod is deployed. After the preparations are complete, the init container runs to completion and exits, and the container to be deployed will be started.

-  **Scenario 1: Wait for other modules to be ready.** For example, an application contains a containerized web server and a database. The web server needs to access the database. However, when the application is started, the database service may have not been started. Therefore, the web server may fail to access the database. To solve this problem, you can use an init container in the pod where the web server is running to check whether the database is ready. The init container runs to completion only when the database is accessible. Then, the web server is started and initiates a formal access request to the database.
-  **Scenario 2: Initialize the configuration.** For example, the init container can check all existing member nodes in the cluster and prepare the cluster configuration information for the main container. After the main container is started, it can be added to the cluster using the configuration information.
-  **Other scenarios**: For example, a pod is registered with a central database and application dependencies are downloaded.

For details, see `Init Containers <https://kubernetes.io/docs/concepts/workloads/pods/init-containers/>`__.

Procedure
---------

#. Edit the YAML file for a workload with an init container enabled.

   .. code-block::

      vi deployment.yaml

   An example YAML file is as follows:

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: mysql
      spec:
        replicas: 1
        selector:
          matchLabels:
            name: mysql
        template:
          metadata:
            labels:
              name: mysql
          spec:
            initContainers:
            - name: getresource
              image: busybox
              command: ['sleep 20']
            containers:
            - name: mysql
              image: percona:5.7.22
              imagePullPolicy: Always
              ports:
              - containerPort: 3306
              resources:
                limits:
                  memory: "500Mi"
                  cpu: "500m"
                requests:
                  memory: "500Mi"
                  cpu: "250m"
              env:
              - name: MYSQL_ROOT_PASSWORD
                value: "mysql"

#. Create the workload.

   .. code-block::

      kubectl create -f deployment.yaml

   Information similar to the following is displayed:

   .. code-block::

      deployment.apps/mysql created

#. Query the created Docker container on the node where the workload is running.

   .. code-block::

      docker ps -a|grep mysql

   The init container will exit after it runs to completion. The query result **Exited (0)** shows the exit status of the init container.

   |image1|

.. |image1| image:: /_static/images/en-us_image_0000002631691662.png
