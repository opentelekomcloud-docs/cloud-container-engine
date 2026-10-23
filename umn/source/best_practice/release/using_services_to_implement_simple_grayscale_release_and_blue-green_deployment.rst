:original_name: cce_bestpractice_10002.html

.. _cce_bestpractice_10002:

Using Services to Implement Simple Grayscale Release and Blue-Green Deployment
==============================================================================

To implement a grayscale release in a CCE cluster, deploy other open-source tools, such as Nginx ingresses, in the cluster or deploy services in service meshes. These solutions are difficult to implement. If your grayscale release requirements are simple and you do not want to introduce too many plug-ins or complex configurations, you can refer to this section to implement simple grayscale release and blue-green deployment based on native Kubernetes features.

Principles
----------

Users usually use Kubernetes objects such as Deployments and StatefulSets to deploy services. Each workload manages a group of pods. The figure below uses a Deployment as an example.

|image1|

Generally, a Service is created for each workload. The Service uses the selector to match the backend pods. Other Services or objects outside the cluster can access the pods backing the Service. If a pod needs to be exposed to external networks, use LoadBalancer Services. The ELB load balancers function as the traffic entry.

-  **Grayscale release principles**

   Take a Deployment as an example. A Service, in most cases, will be created for each Deployment. However, Kubernetes does not require that Services and Deployments correspond to each other. A Service uses a selector to match backend pods. If pods of different Deployments are selected by the same selector, a Service corresponds to multiple versions of Deployments. You can adjust the number of Deployment pods of different versions to adjust the weights of services of different versions to achieve grayscale release. The figure below shows the process.

   |image2|

-  **Blue-green deployment principles**

   Take a Deployment as an example. Two Deployments of different versions have been deployed in the cluster, and their pods are labeled with the same key but different values to distinguish versions. A Service uses the selector to select a Deployment pod of a version. In this case, you can change the value of the label that determines the version in the Service selector to change the pod backing the Service. In this way, you can directly switch the service traffic from one version to another. The figure below shows the process.

   |image3|

Prerequisites
-------------

The Nginx image has been uploaded to SWR. It has two versions: v1 and v2. The welcome pages are **Nginx-v1** and **Nginx-v2**.

Resource Creation
-----------------

You can use YAML to deploy Deployments and Services in either of the following ways:

-  On the **Create Deployment** page, click **Create from YAML** on the right and edit the YAML file in the sliding window.
-  Save the sample YAML file in this section as a file and use kubectl to specify the YAML file. For example, run the **kubectl create -f xxx.yaml** command.

Step 1: Deploy Services of Two Versions
---------------------------------------

The Nginx services of two different versions are deployed in the cluster to provide external access through load balancers.

#. Create a Deployment of the first version. The following uses nginx-v1 as an example. Example YAML:

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: nginx-v1
      spec:
        replicas: 2               # Number of Deployment pods
        selector:                 # Label selector
          matchLabels:
            app: nginx
            version: v1
        template:
          metadata:
            labels:               # Pod label
              app: nginx
              version: v1
          spec:
            containers:
            - image: {your_repository}/nginx:v1 # The image used by the container is nginx:v1.
              name: container-0
              resources:
                limits:
                  cpu: 100m
                  memory: 200Mi
                requests:
                  cpu: 100m
                  memory: 200Mi
            imagePullSecrets:
            - name: default-secret

#. Create a Deployment of the second version. The following uses nginx-v2 as an example. An example YAML file is as follows:

   .. code-block::

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: nginx-v2
      spec:
        replicas: 2               # Number of Deployment pods
        selector:                 # Label selector
          matchLabels:
            app: nginx
            version: v2
        template:
          metadata:
            labels:               # Pod label
              app: nginx
              version: v2
          spec:
            containers:
            - image: {your_repository}/nginx:v2   # The image used by the container is nginx:v2.
              name: container-0
              resources:
                limits:
                  cpu: 100m
                  memory: 200Mi
                requests:
                  cpu: 100m
                  memory: 200Mi
            imagePullSecrets:
            - name: default-secret

   You can log in to the CCE console to view the deployment status.

Step 2: Implement a Grayscale Release
-------------------------------------

#. Create a LoadBalancer Service for the Deployments. Do not specify the version in the selector. Enable the Service to select the pods of the Deployments of two versions. Example YAML:

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        annotations:
          kubernetes.io/elb.id: 586c97da-a47c-467c-a615-bd25a20de39c    # ID of a load balancer. Replace it with the actual value.
        name: nginx
      spec:
        ports:
        - name: service0
          port: 80
          protocol: TCP
          targetPort: 80
        selector:             # The selector does not contain version information.
          app: nginx
         type: LoadBalancer   # Service type (LoadBalancer)

#. Test the connectivity.

   **for i in {1..10}; do curl** <EXTERNAL_IP>\ **; done;**

   <EXTERNAL_IP> indicates the IP address of a load balancer.

   The returned results show that half of the responses were from the Deployment of v1, and the other half were from v2.

   .. code-block::

      Nginx-v2
      Nginx-v1
      Nginx-v1
      Nginx-v1
      Nginx-v2
      Nginx-v1
      Nginx-v2
      Nginx-v1
      Nginx-v2
      Nginx-v2

#. Use the console or kubectl to adjust the number of Deployment pods. Change the number of pods to 4 for Deployment v1 and 1 for Deployment v2.

   **kubectl scale deployment/nginx-v1 --replicas=4**

   **kubectl scale deployment/nginx-v2 --replicas=1**

#. Test the connectivity again.

   **for i in {1..10}; do curl** <EXTERNAL_IP>\ **; done;**

   <EXTERNAL_IP> indicates the IP address of a load balancer.

   The returned results show that, among the 10 access requests, only two responses were from v2. The response ratio of v1 and v2 is the same as the ratio of the number of pods of v1 and v2, that is, 4:1. The grayscale release has been implemented by controlling the number of pods of services of different versions.

   .. code-block::

      Nginx-v1
      Nginx-v1
      Nginx-v1
      Nginx-v1
      Nginx-v2
      Nginx-v1
      Nginx-v2
      Nginx-v1
      Nginx-v1
      Nginx-v1

   .. note::

      If the ratio of v1 to v2 is not 4:1, you can set the number of access times to a larger value, for example, 20. Theoretically, the more the times, the closer the response ratio between v1 and v2 is to 4:1.

Step 3: Implement Blue-Green Deployment
---------------------------------------

#. Create a LoadBalancer Service for a deployed Deployment and specify that v1 is used. Example YAML:

   .. code-block::

      apiVersion: v1
      kind: Service
      metadata:
        annotations:
          kubernetes.io/elb.id: 586c97da-a47c-467c-a615-bd25a20de39c    # ID of a load balancer. Replace it with the actual value.
        name: nginx
      spec:
        ports:
        - name: service0
          port: 80
          protocol: TCP
          targetPort: 80
        selector:             # Set the version to v1 in the selector.
          app: nginx
          version: v1
        type: LoadBalancer    # Service type (LoadBalancer)

#. Test the connectivity.

   **for i in {1..10}; do curl** <EXTERNAL_IP>\ **; done;**

   <EXTERNAL_IP> indicates the IP address of a load balancer.

   The returned results show that all the responses were from v1.

   .. code-block::

      Nginx-v1
      Nginx-v1
      Nginx-v1
      Nginx-v1
      Nginx-v1
      Nginx-v1
      Nginx-v1
      Nginx-v1
      Nginx-v1
      Nginx-v1

#. Use the console or kubectl to modify the selector of the Service so that v2 can be selected.

   **kubectl patch service nginx -p '{"spec":{"selector":{"version":"v2"}}}'**

#. Test the connectivity again.

   **for i in {1..10}; do curl** <EXTERNAL_IP>\ **; done;**

   <EXTERNAL_IP> indicates the IP address of a load balancer.

   The returned results show that all the responses were from v2. The blue-green deployment has been implemented.

   .. code-block::

      Nginx-v2
      Nginx-v2
      Nginx-v2
      Nginx-v2
      Nginx-v2
      Nginx-v2
      Nginx-v2
      Nginx-v2
      Nginx-v2
      Nginx-v2

.. |image1| image:: /_static/images/en-us_image_0000002631691522.png
.. |image2| image:: /_static/images/en-us_image_0000002631531622.png
.. |image3| image:: /_static/images/en-us_image_0000002631691520.png
