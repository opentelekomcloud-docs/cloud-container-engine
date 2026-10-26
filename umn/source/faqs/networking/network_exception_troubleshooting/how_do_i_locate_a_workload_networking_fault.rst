:original_name: cce_faq_00202.html

.. _cce_faq_00202:

How Do I Locate a Workload Networking Fault?
============================================

Troubleshooting
---------------

Possible causes are listed in order of likelihood.

Check these causes one by one until you find the cause of the fault.

-  :ref:`Check Item 1: Container and Container Port <cce_faq_00202__section11960929145416>`
-  :ref:`Check Item 2: Node IP Address and NodePort <cce_faq_00202__section138152218598>`
-  :ref:`Check Item 3: Load Balancer IP Address and Service Port <cce_faq_00202__section3966114610915>`
-  :ref:`Check Item 4: NAT Gateway and Port <cce_faq_00202__section77791227101111>`
-  :ref:`Check Item 5: Whether the Security Group of the Node Where the Container Is Located Allows Access <cce_faq_00202__section6115457143415>`

.. _cce_faq_00202__section11960929145416:

Check Item 1: Container and Container Port
------------------------------------------

Log in to the CCE console or use kubectl to obtain the pod IP address. Then, log in to the node or the pod and run **curl** or use another method to manually call the API and check whether the expected response is returned.

If the API is inaccessible externally, log in to the service container and attempt to access **127.0.0.1**:*{port}*.

**Common Issues**

#. The container port is incorrectly configured (the container does not listen to the access port).
#. The URL does not exist (no related path exists in the container).
#. A Service exception (a Service bug in the container) occurs.
#. Check whether the cluster network's kernel component is functioning correctly (Open vSwitch for the container tunnel network model, IPvlan for the VPC network model, and the component for the Cloud Native Network 2.0 model).

.. _cce_faq_00202__section138152218598:

Check Item 2: Node IP Address and NodePort
------------------------------------------

Only NodePort or LoadBalancer Services can be accessed externally using the node IP address and NodePort.

-  **NodePort Services:**

   The NodePort is exposed through the node's security group.

-  **LoadBalancer Service:**

   The NodePort can be viewed in the Service YAML.

Example:

**nodePort: 30637** indicates the exposed node port. **targetPort: 80** indicates the exposed pod port. **port: 123** is the exposed Service port. LoadBalancer Services also use this port to configure the ELB listener.

|image1|

After identifying the NodePort, access *<node-IP-address>:<NodePort>* of the node where the container is running and check whether the expected response is returned.

**Common Issues**

#. The NodePort is not allowed in the inbound rules of the node's security group.
#. A custom route is incorrectly configured for the node.
#. Pod labels do not match the Service selector (regardless of whether the Service was created via kubectl or API).

.. _cce_faq_00202__section3966114610915:

Check Item 3: Load Balancer IP Address and Service Port
-------------------------------------------------------

There are several possible causes if <load-balancer-IP-address>:<Service-port> is inaccessible while <node-IP-address>:<NodePort> works.

**Possible causes:**

-  The backend server group of the port or URL does not meet the expectations.
-  The security group on the node has not exposed the related protocol or port to the ELB.
-  The health check of the Layer 4 load balancing is not enabled.
-  The certificate used for Services of Layer 7 load balancing has expired.

**Common Issues**

#. When exposing a Layer 4 ELB load balancer, if you have not enabled health check on the console, the load balancer may route requests to abnormal nodes.
#. For UDP access, the ICMP port of the node has not been allowed in the inbound rules.
#. Pod labels do not match the Service selector (regardless of whether the Service was created via kubectl or API).

.. _cce_faq_00202__section77791227101111:

Check Item 4: NAT Gateway and Port
----------------------------------

Generally, no EIP is configured for the backend server of NAT. Otherwise, exceptions such as network packet loss may occur.

.. _cce_faq_00202__section6115457143415:

Check Item 5: Whether the Security Group of the Node Where the Container Is Located Allows Access
-------------------------------------------------------------------------------------------------

Log in to the management console and choose **Service List** > **Networking** > **Virtual Private Cloud**. On the Network console, choose **Access Control** > **Security Groups**, locate the security group rule of the CCE cluster, and modify and strengthen the security group rule.

-  CCE cluster:

   The security group name of the node is **{Cluster name}-cce-node-{Random characters}**.

-  CCE Turbo cluster:

   The security group name of the node is **{Cluster name}-cce-node-{Random characters}**.

   The name of the security group associated with the containers is **{Cluster name}-cce-eni-{Random characters}**.

**Check the following:**

-  IP address, port, and protocol of an external request to access the workloads in the cluster. They must be allowed in the inbound rules of the cluster security group.
-  IP address, port, and protocol of a request sent by a workload to visit external applications outside the cluster. They must be allowed in the outbound rules of the cluster security group.

For details about security group configuration, see :ref:`How Do I Modify Cluster Security Group Rules? <cce_faq_00265>`

.. |image1| image:: /_static/images/en-us_image_0000002661851415.png
