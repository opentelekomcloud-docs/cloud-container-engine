:original_name: cce_10_0916.html

.. _cce_10_0916:

Enabling a LoadBalancer Service to Obtain the Client IP Address
===============================================================

When creating a LoadBalancer Service using a shared load balancer, you can configure annotations for the load balancer listeners to obtain the client IP address.

.. note::

   -  If a dedicated load balancer is used, the function of obtaining the client IP address is enabled by default.
   -  After the function of obtaining the client IP address is enabled, if you delete the target annotation from the YAML file, the configuration on the ELB will be retained.

Prerequisites
-------------

-  A Kubernetes cluster is available and the cluster version meets the following requirements:

   -  v1.23: v1.23.17-r0 or later
   -  v1.25: v1.25.12-r0 or later
   -  v1.27: v1.27.9-r0 or later
   -  v1.28: v1.28.7-r0 or later
   -  v1.29: v1.29.3-r0 or later
   -  Other clusters of later versions

-  The cluster can be accessed using kubectl. For details, see :ref:`Accessing a Cluster Using kubectl <cce_10_0107>`.

Notes and Constraints
---------------------

In CCE standard clusters, this setting takes effect only when Service affinity is set to the node level affinity (with **externalTrafficPolicy** set to **Local**).

In CCE Turbo clusters, this setting cannot take effect, because Service affinity cannot be set to the node level affinity and **externalTrafficPolicy** cannot be set to **Local**.

Using kubectl
-------------

An example YAML file of a Service created using an existing load balancer is as follows:

.. code-block::

   apiVersion: v1
   kind: Service
   metadata:
     name: nginx
     annotations:
       kubernetes.io/elb.id: <your_elb_id>              # Load balancer ID. Replace it with the actual value.
       kubernetes.io/elb.class: union                   # Load balancer type
       kubernetes.io/elb.transparent-client-ip: 'true'  # Enable the function of obtaining the client source IP address.
   spec:
     selector:
        app: nginx
     externalTrafficPolicy: Local
     ports:
     - name: service0
       port: 80
       protocol: TCP
       targetPort: 80
     type: LoadBalancer

.. table:: **Table 1** Key parameters

   +-----------------------------------------+-----------------------+---------------------------------------------------------------------------------+
   | Parameter                               | Type                  | Description                                                                     |
   +=========================================+=======================+=================================================================================+
   | kubernetes.io/elb.transparent-client-ip | String                | This parameter can be configured only for TCP/UDP Services.                     |
   |                                         |                       |                                                                                 |
   |                                         |                       | -  **true**: Enabling the function of obtaining the client source IP address.   |
   |                                         |                       | -  **false**: Disabling the function of obtaining the client source IP address. |
   +-----------------------------------------+-----------------------+---------------------------------------------------------------------------------+
