:original_name: cce_faq_00099.html

.. _cce_faq_00099:

How Do I Use kubectl to Set the Workload Access Type to LoadBalancer (ELB)?
===========================================================================

This section uses the Nginx workload as an example to describe how to set the workload access type to **LoadBalancer (ELB)**.

Prerequisites
-------------

-  An ELB has been created.
-  You have connected an Elastic Cloud Server (ECS) to the cluster by running the kubectl command.

Procedure
---------

#. Log in to the ECS on which the kubectl has been configured.

#. Create and edit the **nginx-deployment.yaml** and **nginx-elb-svc.yaml** files.

   The file names are user-defined. **nginx-deployment.yaml** and **nginx-elb-svc.yaml** are merely example file names.

   **vi nginx-deployment.yaml**

   .. code-block::

      apiVersion: extensions/v1beta1
      kind: Deployment
      metadata:
        name: nginx
      spec:
        replicas: 1
        selector:
          matchLabels:
            app: nginx
        strategy:
          type: RollingUpdate
        template:
          metadata:
            labels:
              app: nginx
          spec:
            containers:
            - image: nginx
              imagePullPolicy: Always
              name: nginx
            imagePullSecrets:
            - name: default-secret

   **vi nginx-elb-svc.yaml**

   .. note::

      Before enabling session stickness, ensure that the following conditions are met:

      -  The workload protocol is TCP.
      -  Anti-affinity has been configured between pods of the workload. That is, all pods of the workload are deployed on different nodes.

   -  Automatically creating load balancer

      .. code-block::

         apiVersion: v1
         kind: Service
         metadata:
           annotations:
             kubernetes.io/elb.class: union
             kubernetes.io/session-affinity-mode: SOURCE_IP
             kubernetes.io/elb.subnet-id: 5083f225-9bf8-48fa-9c8b-67bd9693c4c0
             kubernetes.io/elb.enterpriseID: debb7ae2-6d2f-4e6c-a0aa-1ccafd92b8eb
             kubernetes.io/elb.autocreate: '{"type":"public","bandwidth_name":"cce-bandwidth-1551163379627","bandwidth_chargemode":"traffic","bandwidth_size":5,"bandwidth_sharetype":"PER","eip_type":"5_bgp","name":"james"}'
           labels:
             app: nginx
           name: nginx
         spec:
           externalTrafficPolicy: Local
           ports:
           - name: service0
             port: 80
             protocol: TCP
             targetPort: 80
           selector:
             app: nginx
           type: LoadBalancer

   -  Using existing load balancer

      .. code-block::

         apiVersion: v1
         kind: Service
         metadata:
           annotations:
             kubernetes.io/elb.class: union
             kubernetes.io/session-affinity-mode: SOURCE_IP
             kubernetes.io/elb.id: 3c7caa5a-a641-4bff-801a-feace27424b6
             kubernetes.io/elb.subnet-id: 5083f225-9bf8-48fa-9c8b-67bd9693c4c0
           labels:
             app: nginx
           name: nginx
         spec:
           loadBalancerIP: 10.78.42.242
           externalTrafficPolicy: Local
           ports:
           - name: service0
             port: 80
             protocol: TCP
             targetPort: 80
           selector:
             app: nginx
           type: LoadBalancer

   .. table:: **Table 1** Key parameters

      +-------------------------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | Parameter                           | Type                                                            | Description                                                                                                                                                                                                                                                                                                              |
      +=====================================+=================================================================+==========================================================================================================================================================================================================================================================================================================================+
      | kubernetes.io/elb.class             | String                                                          | Mandatory and must be set to **union** if an enhanced load balancer is in use.                                                                                                                                                                                                                                           |
      +-------------------------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/session-affinity-mode | String                                                          | Optional. If session stickness is enabled, add this parameter.                                                                                                                                                                                                                                                           |
      |                                     |                                                                 |                                                                                                                                                                                                                                                                                                                          |
      |                                     |                                                                 | The value **SOURCE_IP** indicates that listeners ensure session stickiness based on source IP addresses.                                                                                                                                                                                                                 |
      +-------------------------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.id                | String                                                          | Optional. This parameter is mandatory if an existing load balancer is used.                                                                                                                                                                                                                                              |
      |                                     |                                                                 |                                                                                                                                                                                                                                                                                                                          |
      |                                     |                                                                 | It indicates the ID of an enhanced load balancer.                                                                                                                                                                                                                                                                        |
      +-------------------------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.subnet-id         | String                                                          | Optional. This parameter is mandatory only if a load balancer will be automatically created. For clusters of v1.11.7-r0 or later, this parameter can be left unspecified.                                                                                                                                                |
      +-------------------------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.enterpriseID      | String                                                          | Optional. This parameter is mandatory if a public/private network load balancer will be automatically created.                                                                                                                                                                                                           |
      |                                     |                                                                 |                                                                                                                                                                                                                                                                                                                          |
      |                                     |                                                                 | This parameter indicates the name of the ELB enterprise project in which the ELB will be created.                                                                                                                                                                                                                        |
      +-------------------------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | kubernetes.io/elb.autocreate        | :ref:`elb.autocreate <cce_faq_00099__table957018613817>` object | Optional. This parameter is mandatory if a public network load balancer will be automatically created. The system will create an enhanced load balancer and an EIP. This parameter is also mandatory if a private network load balancer will be automatically created. The system will create an enhanced load balancer. |
      |                                     |                                                                 |                                                                                                                                                                                                                                                                                                                          |
      |                                     |                                                                 | **Example:**                                                                                                                                                                                                                                                                                                             |
      |                                     |                                                                 |                                                                                                                                                                                                                                                                                                                          |
      |                                     |                                                                 | -  Automatically created public network load balancer:                                                                                                                                                                                                                                                                   |
      |                                     |                                                                 |                                                                                                                                                                                                                                                                                                                          |
      |                                     |                                                                 |    {"type":"public","bandwidth_name":"cce-bandwidth-1551163379627","bandwidth_chargemode":"traffic","bandwidth_size":5,"bandwidth_sharetype":"PER","eip_type":"5_bgp","name":"james"}                                                                                                                                    |
      |                                     |                                                                 |                                                                                                                                                                                                                                                                                                                          |
      |                                     |                                                                 | -  Automatically created private network load balancer:                                                                                                                                                                                                                                                                  |
      |                                     |                                                                 |                                                                                                                                                                                                                                                                                                                          |
      |                                     |                                                                 |    {"type":"inner", "name": "A-location-d-test"}                                                                                                                                                                                                                                                                         |
      +-------------------------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | loadBalancerIP                      | String                                                          | Private IP address of a private network load balancer or public IP address of a public network load balancer.                                                                                                                                                                                                            |
      +-------------------------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | externalTrafficPolicy               | String                                                          | Optional. If session stickness is enabled, add this parameter so requests are transferred to a fixed node. If a LoadBalancer service with this parameter set to **Local** is created for a workload, the workload can be accessed only when the client is installed on the same node as the server.                      |
      +-------------------------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | port                                | Integer                                                         | Access port that is registered on the load balancer and mapped to the cluster-internal IP address.                                                                                                                                                                                                                       |
      +-------------------------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
      | targetPort                          | String                                                          | Container port on the CCE console.                                                                                                                                                                                                                                                                                       |
      +-------------------------------------+-----------------------------------------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

   .. _cce_faq_00099__table957018613817:

   .. table:: **Table 2** elb.autocreate parameters

      +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------+
      | Parameter             | Type                  | Description                                                                                                                 |
      +=======================+=======================+=============================================================================================================================+
      | name                  | String                | Name of the load balancer that is automatically created.                                                                    |
      |                       |                       |                                                                                                                             |
      |                       |                       | The value is a string of 1 to 64 characters that consist of letters, digits, underscores (_), and hyphens (-).              |
      +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------+
      | type                  | String                | Network type of the load balancer.                                                                                          |
      |                       |                       |                                                                                                                             |
      |                       |                       | -  **public**: public network load balancer.                                                                                |
      |                       |                       | -  **inner**: private network load balancer.                                                                                |
      +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------+
      | bandwidth_name        | String                | Bandwidth name. The default value is **cce-bandwidth-*****\***.                                                             |
      |                       |                       |                                                                                                                             |
      |                       |                       | The value is a string of 1 to 64 characters that consist of letters, digits, underscores (_), hyphens (-), and periods (.). |
      +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------+
      | bandwidth_chargemode  | String                | Bandwidth billing mode.                                                                                                     |
      |                       |                       |                                                                                                                             |
      |                       |                       | The value is **traffic**, indicating that the billing is based on traffic.                                                  |
      +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------+
      | bandwidth_size        | Integer               | Bandwidth size. Set this parameter based on the bandwidth range supported by the region.                                    |
      +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------+
      | bandwidth_sharetype   | String                | Bandwidth sharing mode.                                                                                                     |
      |                       |                       |                                                                                                                             |
      |                       |                       | -  **PER**: dedicated bandwidth.                                                                                            |
      |                       |                       | -  **WHOLE**: shared bandwidth.                                                                                             |
      +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------+
      | eip_type              | String                | EIP type.                                                                                                                   |
      +-----------------------+-----------------------+-----------------------------------------------------------------------------------------------------------------------------+

#. Create a workload.

   **kubectl create -f nginx-deployment.yaml**

   If information similar to the following is displayed, the workload is being created.

   .. code-block::

      deployment "nginx" created

   **kubectl get po**

   If information similar to the following is displayed, the workload is running.

   .. code-block::

      NAME                     READY     STATUS             RESTARTS   AGE
      etcd-0                   0/1       ImagePullBackOff   0          1h
      icagent-m9dkt            0/0       Running            0          3d
      nginx-2601814895-c1xhw   1/1       Running            0          6s

#. Create a service.

   **kubectl create -f nginx-elb-svc.yaml**

   If information similar to the following is displayed, the service has been created.

   .. code-block::

      service "nginx" created

   **kubectl get svc**

   If information similar to the following is displayed, the service access type has been set successfully, and the workload is accessible.

   .. code-block::

      NAME         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
      etcd-svc     ClusterIP      None             <none>        3120/TCP       1h
      kubernetes   ClusterIP      10.247.0.1       <none>        443/TCP        3d
      nginx        LoadBalancer   10.247.130.196   10.78.42.242   80:31540/TCP   51s

#. Enter the URL in the address box of the browser, for example, **10.78.42.242:31540**. **10.78.42.242** indicates the IP address of the load balancer, and **31540** indicates the access port displayed on the CCE console.
