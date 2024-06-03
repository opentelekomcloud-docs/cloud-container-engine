:original_name: cce_bestpractice_00228.html

.. _cce_bestpractice_00228:

Using Init Containers to Initialize an Application
==================================================

Concepts
--------

Before containers running applications are started, one or some init containers are started first. If there are multiple init containers, they will be started in the defined sequence. The application containers are started only after all init containers run to completion and exit. Storage volumes in a pod are shared. Therefore, the data generated in the init containers can be used by the application containers.

Init containers can be used in multiple Kubernetes resources, such as Deployments, DaemonSets, and jobs. They perform initialization before application containers are started.

Application Scenarios
---------------------

Before deploying a service, you can use an init container to make preparations before the pod where the service is running is deployed. After the preparations are complete, the init container runs to completion and exit, and the container to be deployed will be started.

-  **Scenario 1: Wait for other modules to be ready.** For example, an application contains two containerized services: web server and database. The web server service needs to access the database service. However, when the application is started, the database service may have not been started. Therefore, web server may fail to access database. To solve this problem, you can use an init container in the pod where web server is running to check whether database is ready. The init container runs to completion only when database is accessible. Then, web server is started and initiates a formal access request to database.
-  **Scenario 2: Initialize the configuration.** For example, the init container can check all existing member nodes in the cluster and prepare the cluster configuration information for the application container. After the application container is started, it can be added to the cluster using the configuration information.
-  **Other scenarios**: For example, register a pod with a central database and download application dependencies.

For details, see `Init Containers <https://kubernetes.io/docs/concepts/workloads/pods/init-containers/>`__.

Procedure
---------

#. Edit the YAML file of the init container workload.

   **vi deployment.yaml**

   An example YAML file is provided as follows:

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

#. Create an init container workload.

   **kubectl create -f deployment.yaml**

   Information similar to the following is displayed:

   .. code-block::

      deployment.apps/mysql created

#. Query the created Docker container on the node where the workload is running.

   **docker ps -a|grep mysql**

   The init container will exit after it runs to completion. The query result **Exited (0)** shows the exit status of the init container.

   |image1|

.. |image1| image:: /_static/images/en-us_image_0000001897905125.png
