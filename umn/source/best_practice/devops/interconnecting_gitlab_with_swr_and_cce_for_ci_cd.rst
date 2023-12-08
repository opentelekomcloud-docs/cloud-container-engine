:original_name: cce_bestpractice_0324.html

.. _cce_bestpractice_0324:

Interconnecting GitLab with SWR and CCE for CI/CD
=================================================

Background
----------

GitLab is an open-source version management system developed with Ruby on Rails for Git project repository management. It supports web-based access to public and private projects. Similar to GitHub, GitLab allows you to browse source code, manage bugs and comments, and control team member access to repositories. You will find it very easy to view committed versions and file history database. Team members can communicate with each other using the built-in chat program (Wall).

GitLab provides powerful CI/CD functions and is widely used in software development.


.. figure:: /_static/images/en-us_image_0000001701704293.png
   :alt: **Figure 1** GitLab CI/CD process

   **Figure 1** GitLab CI/CD process

This section describes how to interconnect GitLab with SWR and CCE for CI/CD.

Preparations
------------

#. Create a CCE cluster and a node and bind an EIP to the node for downloading an image during GitLab Runner installation.
#. Download and configure kubectl to connect to the cluster.
#. `Install Helm 3 <https://helm.sh/docs/intro/install/>`__.

Installing GitLab Runner
------------------------

Log in to `GitLab <https://www.gitlab.com/>`__, choose **Settings** > **CI/CD** in the project view, click **Expand** next to **Runners**, and search for the GitLab Runner registration URL and token.

|image1|

Create the **values.yaml** file and fill in the following information:

.. code-block::

   # Registration URL
   gitlabUrl: https://gitlab.com/
   # Registration token
   runnerRegistrationToken: "GR13489411dKVzmTyaywEDTF_1QXb"
   rbac:
       create: true
   runners:
       privileged: true

Create a GitLab namespace.

.. code-block::

   kubectl create namespace gitlab

Install GitLab Runner using Helm.

.. code-block::

   helm repo add gitlab https://charts.gitlab.io
   helm install --namespace gitlab gitlab-runner -f values.yaml gitlab/gitlab-runner --version=0.43.1

After the installation, you can obtain the gitlab-runner workload on the CCE console and view the connection information in GitLab later.

|image2|

Creating an Application
-----------------------

Place the application to be created in the GitLab project repository. This section takes Nginx modification as an example. For details, visit https://gitlab.com/c8147/cidemo/-/tree/main.

The following files are included:

-  **.gitlab-ci.yml**: Gitlab CI file, which will be described in detail in :ref:`Creating a Pipeline <cce_bestpractice_0324__section171541431101910>`.
-  **Dockerfile**: used to build Docker images.
-  **index.html**: used to replace the index page of Nginx.
-  **k8s.yaml**: used to deploy the Nginx app. A Deployment named **nginx-test** and a Service named **nginx-test** will be created.

The preceding files are only examples. You can replace or modify them accordingly.

Configuring Global Variables
----------------------------

When using pipelines, build an image, upload it to SWR, and run kubectl commands to deploy the image in the cluster. Before performing these operations, you must log in to SWR and obtain the credential for connecting to the cluster. You can define the information as variables in GitLab.

Log in to `GitLab <https://www.gitlab.com/>`__, choose **Settings** > **CI/CD** in the project view, and click **Expand** next to **Variables** to add variables.

|image3|

-  **kube_config**

   **kubeconfig.json** file used for kubectl command authentication. Run the following command on the host where kubectl is configured to convert the file to the Base64 format:

   **echo $(cat ~/.kube/config \| base64) \| tr -d " "**

   The command output is the content of **kubeconfig.json**.

-  **project**: project name.

   Log in to the management console, click your username in the upper right corner, and click **My Credentials**. In the **Projects** area on the **API Credentials** page, check the name of the project in your current region.

-  **swr_ak**: access key.

   Log in to the management console, click your username in the upper right corner, and click **My Credentials**. In the navigation pane, choose **Access Keys**. Click **Create Access Key**, enter the description, and click **OK**. In the displayed **Information** dialog box, click **Download**. After the certificate is downloaded, obtain the AK and SK information from the **credentials** file.

-  **swr_sk**: secret key for logging in to SWR.

   Run the following command to obtain the key pair. Replace *$AK* and *$SK* with the AK and SK obtained in the preceding steps.

   **printf "$AK" \| openssl dgst -binary -sha256 -hmac "$SK" \| od -An -vtx1 \| sed 's/[ \\n]//g' \| sed 'N;s/\\n//'**

   The command output displays the login key pair.

.. _cce_bestpractice_0324__section171541431101910:

Creating a Pipeline
-------------------

Log in to `Gitlab <https://www.gitlab.com/>`__ and add the **.gitlab-ci.yml** file to **Repository**.

|image4|

The content is as follows:

.. code-block::

   # Define pipeline stages, including package, build, and deploy.
   stages:
     - package
     - build
     - deploy
   # If no image is specified in each stage, the default image docker:latest is used.
   image: docker:latest
   # In the package stage, only printing is performed.
   package:
     stage: package
     script:
       - echo "package"
   # In the build stage, the Docker-in-Docker mode is used.
   build:
     stage: build
     # Define environment variables for the build stage.
     variables:
       DOCKER_HOST: tcp://docker:2375
     # Define the image for running Docker-in-Docker.
     services:
       - docker:18.09-dind
     script:
       - echo "build"
       # Log in to SWR.
       - docker login -u $project@$swr_ak -p $swr_sk swr.eu-de.otc.t-systems.com
       # Build an image. k8s-dev is the organization name in SWR. Replace it to the actual name.
       - docker build -t swr.eu-de.otc.t-systems.com/k8s-dev/nginx:$CI_PIPELINE_ID .
       # Push the image to SWR.
       - docker push swr.eu-de.otc.t-systems.com/k8s-dev/nginx:$CI_PIPELINE_ID
   deploy:
     # Use the kubectl image.
     image:
       name: bitnami/kubectl:latest
       entrypoint: [""]
     stage: deploy
     script:
       # Configure the kubeconfig file.
       - mkdir -p $HOME/.kube
       - export KUBECONFIG=$HOME/.kube/config
       - echo $kube_config |base64 -d > $KUBECONFIG
       # Replace the image in the k8s.yaml file.
       - sed -i "s/<IMAGE_NAME>/swr.eu-de.otc.t-systems.com\/k8s-dev\/nginx:$CI_PIPELINE_ID/g" k8s.yaml
       - cat k8s.yaml
       # Deploy an application.
       - kubectl apply -f k8s.yaml

After the **.gitlab-ci.yml** file is saved, the pipeline is started immediately. You can view the pipeline execution status in GitLab.

|image5|

Verifying Deployment
--------------------

After the pipeline is deployed, locate the **nginx-test** Service on the CCE console, query its access address, and run the **curl** command to access the Service.

.. code-block::

   # curl xxx.xxx.xxx.xxx:31111
   Hello Gitlab!

If the preceding information is displayed, the deployment is correct.

FAQs
----

-  If the following problem occurs during the deployment:

   |image6|

   Or

   |image7|

   Check whether the following commands are missing in the **.gitlab-ci.yml** file. If yes, add them to the **.gitlab-ci.yml** file.

   .. code-block::

      ...
      deploy:
        # Use the kubectl image.
        image:
          name: bitnami/kubectl:latest
          entrypoint: [""]
        stage: deploy
        script:
          # Configure the kubeconfig file.
          - mkdir -p $HOME/.kube
          - export KUBECONFIG=$HOME/.kube/config
          - echo $kube_config |base64 -d > $KUBECONFIG
          # Replace the image in the k8s.yaml file.
      ...

-  If Docker cannot be executed, information similar to the following will display.

   |image8|

   The **privileged: true** parameter fails to be transferred during GitLab Runner installation. As a result, you do not have the permission to run the docker command. To resolve this issue, find GitLab Runner in the workload list on the CCE console, add the environment variable **KUBERNETES_PRIVILEGED**, and set its value to **true**.

   |image9|

.. |image1| image:: /_static/images/en-us_image_0000001701785025.png
.. |image2| image:: /_static/images/en-us_image_0000001701704301.png
.. |image3| image:: /_static/images/en-us_image_0000001653584808.png
.. |image4| image:: /_static/images/en-us_image_0000001653425500.png
.. |image5| image:: /_static/images/en-us_image_0000001701785049.png
.. |image6| image:: /_static/images/en-us_image_0000001701704289.png
.. |image7| image:: /_static/images/en-us_image_0000001653584820.png
.. |image8| image:: /_static/images/en-us_image_0000001653584824.png
.. |image9| image:: /_static/images/en-us_image_0000001667910920.png
