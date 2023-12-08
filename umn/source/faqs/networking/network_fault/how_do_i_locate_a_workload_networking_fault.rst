:original_name: cce_faq_00202.html

.. _cce_faq_00202:

How Do I Locate a Workload Networking Fault?
============================================

Troubleshooting Process
-----------------------

Troubleshooting methods are sorted based on the occurrence probability of the possible causes. You are advised to check the possible causes from high probability to low probability to quickly locate the cause of the problem.

If the fault persists after a possible cause is rectified, check other possible causes.

-  :ref:`Check Item 1: Container and Container Port <cce_faq_00202__section11960929145416>`
-  :ref:`Check Item 2: Node IP Address and Node Port <cce_faq_00202__section138152218598>`
-  :ref:`Check Item 3: ELB IP Address and Port <cce_faq_00202__section3966114610915>`
-  :ref:`Check Item 4: NAT Gateway + Port <cce_faq_00202__section77791227101111>`
-  :ref:`Check Item 5: Whether the Security Group of the Node Where the Container Is Located Allows Access <cce_faq_00202__section6115457143415>`

.. _cce_faq_00202__section11960929145416:

Check Item 1: Container and Container Port
------------------------------------------

Log in to the CCE console or use kubectl to query the IP address of the pod. Then, log in to the node or container in the cluster and run the **curl** command to manually call the API. Check whether the expected result is returned.

If <container IP address>:<port> cannot be accessed, you are advised to log in to the application container and access <127.0.0.1>:<port> to locate the fault.

**Common issues:**

#. The container port is incorrectly configured (the container does not listen to the access port).
#. The URL does not exist (no related path exists in the container).
#. A Service exception (a Service bug in the container) occurs.
#. Check whether the cluster network kernel component is abnormal (container tunnel network model: openswitch kernel component; VPC network model: ipvlan kernel component).

.. _cce_faq_00202__section138152218598:

Check Item 2: Node IP Address and Node Port
-------------------------------------------

Only NodePort or LoadBalancer Services can be accessed using the node IP address and node port.

-  **NodePort Services:**

   The access port of a node is the port exposed externally by the node.

-  **LoadBalancer Service:**

   You can view the node port of a LoadBalancer Service by editing the YAML file.

Example:

**nodePort: 30637** indicates the exposed node port. **targetPort: 80** indicates the exposed pod port. **port: 123** is the exposed Service port. LoadBalancer Services also use this port to configure the ELB listener.

|image1|

After finding the node port (nodePort), access <IP address>:<port> of the node where the container is located and check whether the expected result is returned.

**Common issues:**

#. The service port is not allowed in the inbound rules of the node.
#. A custom route is incorrectly configured for the node.
#. The label of the pod does not match that of the Service (created using kubectl or API).

.. _cce_faq_00202__section3966114610915:

Check Item 3: ELB IP Address and Port
-------------------------------------

There are several possible causes if <IP address>:<port> of the ELB cannot be accessed, but <IP address>:<port> of the node can be accessed.

**Possible causes:**

-  The backend server group of the port or URL does not meet the expectation.
-  The security group on the node has not exposed the related protocol or port to the ELB.
-  The health check of the layer-4 load balancing is not enabled.
-  The certificate used for Services of layer-7 load balancing has expired.

**Common issues:**

#. When exposing a layer-4 ELB load balancer, if you have not enabled health check on the console, the load balancer may route requests to abnormal nodes.
#. For UDP access, the ICMP port of the node has not been allowed in the inbound rules.
#. The label of the pod does not match that of the Service (created using kubectl or API).

.. _cce_faq_00202__section77791227101111:

Check Item 4: NAT Gateway + Port
--------------------------------

Generally, no EIP is configured for the backend server of NAT. Otherwise, exceptions such as network packet loss may occur.

.. _cce_faq_00202__section6115457143415:

Check Item 5: Whether the Security Group of the Node Where the Container Is Located Allows Access
-------------------------------------------------------------------------------------------------

Log in to the management console, choose **Service List** > **Networking** > **Virtual Private Cloud**. On the Network console, choose **Access Control** > **Security Groups**, locate the security group rule of the CCE cluster, and modify and harden the security group rule.

-  CCE cluster:

   The security group name of the node is **{Cluster name}-cce-node-{Random characters}**.

-  CCE Turbo cluster:

   The security group name of the node is **{Cluster name}-cce-node-{Random characters}**.

   The name of the security group associated with the containers is **{Cluster name}-cce-eni-{Random characters}**.

**Check the following:**

-  IP address, port, and protocol of an external request to access the workloads in the cluster. They must be allowed in the inbound rule of the cluster security group.
-  IP address, port, and protocol of a request by a workload to visit external applications outside the cluster. They must be allowed in the outbound rule of the cluster security group.

For details about security group configuration, see :ref:`Configuring Cluster Security Group Rules <cce_faq_00265>`.

.. |image1| image:: /_static/images/en-us_image_0000001704574297.png
