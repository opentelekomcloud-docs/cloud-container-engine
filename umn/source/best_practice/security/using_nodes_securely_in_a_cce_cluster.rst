:original_name: cce_bestpractice_0318.html

.. _cce_bestpractice_0318:

Using Nodes Securely in a CCE Cluster
=====================================

Preventing Nodes from Being Exposed to the Internet
---------------------------------------------------

-  Do not bind an EIP to a node to reduce the attack surface unless necessary.
-  If an EIP must be used, properly configure the firewall or security group rules to restrict access of unnecessary ports and IP addresses.

You may have configured the **kubeconfig.json** file on a node in your cluster. kubectl can use the certificate and private key in this file to control the entire cluster. You are advised to delete unnecessary files from the **/root/.kube** directory on the node to prevent malicious use.

rm -rf /root/.kube

Hardening VPC Security Group Rules
----------------------------------

CCE is a universal container platform. Its default security group rules apply to general scenarios. You can harden the security group rules set for CCE clusters based on security requirements on the **Security Groups** page of **Network Console**.

Hardening Node Security on Demand
---------------------------------

CCE cluster nodes use the default settings of open-source OSs. After a node is created, you need to perform security hardening according to your service requirements.

You can use either of the following ways to harden node security on CCE:

-  Use the post-installation script after the node is created. For details, see the description about **Post-installation Script** in **Advanced Settings** during node creation. This script is user-defined.

Forbidding Containers to Obtain the Node Metadata
-------------------------------------------------

If multiple users deploy containers in the same CCE cluster, ensure that the containers cannot access the OpenStack management address (169.254.169.254) to prevent them from obtaining the node metadata.

For details about how to restore the metadata, see the "Notes" section in `Obtaining ECS Details Using Metadata <https://docs.otc.t-systems.com/en-us/usermanual/ecs/en-us_topic_0042400609.html>`__.

.. warning::

   This restoration may affect the password change on the ECS console. Therefore, you must verify the function before the restoration.

#. Obtain the network model and container CIDR of the cluster.

   On the **Clusters** page of the CCE console, view the network model and container CIDR of the cluster.

#. Prevent the container from obtaining the node metadata.

   -  In a cluster using a VPC network

      a. Log in to each node in the cluster as user **root** and run the following command:

         .. code-block::

            iptables -I OUTPUT -s {container_cidr} -d 169.254.169.254 -j REJECT

         *{container_cidr}* indicates the container CIDR block of the cluster, for example, **10.0.0.0/16**.

         To ensure configuration persistence, write the command to the **/etc/rc.local** script.

      b. Run the following commands in the container to access the **userdata** and **metadata** interfaces of OpenStack and check whether the request is intercepted:

         .. code-block::

            curl 169.254.169.254/openstack/latest/meta_data.json
            curl 169.254.169.254/openstack/latest/user_data

   -  In a cluster using a container tunnel network

      a. Log in to each node in the cluster as user **root** and run the following command:

         .. code-block::

            iptables -I FORWARD -s {container_cidr} -d 169.254.169.254 -j REJECT

         *{container_cidr}* indicates the container CIDR block of the cluster, for example, **10.0.0.0/16**.

         To ensure configuration persistence, write the command to the **/etc/rc.local** script.

      b. Run the following commands in the container to access the **userdata** and **metadata** interfaces of OpenStack and check whether the request is intercepted:

         .. code-block::

            curl 169.254.169.254/openstack/latest/meta_data.json
            curl 169.254.169.254/openstack/latest/user_data

   -  In a CCE Turbo cluster

      No additional configuration is required for a cluster of a version earlier than v1.23.13-r0, v1.25.8-r0, v1.27.5-r0, or v1.28.3-r0.

      For a cluster of v1.23.13-r0, v1.25.8-r0, v1.27.5-r0, v1.28.3-r0, or a later version, log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Settings**, click the **Network** tab, and view the value of **Pod Access to Metadata**.

      -  If **Pod Access to Metadata** is not enabled, no additional configuration is required. The container cannot obtain the node metadata.
      -  If **Pod Access to Metadata** is enabled, take the following steps to prevent the container from obtaining the node metadata:

         a. Log in to each node in the cluster as user **root** and run the following command:

            .. code-block::

               iptables -I FORWARD -s {container_cidr} -d 169.254.169.254 -j REJECT

            *{container_cidr}* indicates the container CIDR block of the cluster, for example, **10.0.0.0/16**.

            To ensure configuration persistence, write the command to the **/etc/rc.local** script.

         b. Run the following commands in the container to access the **userdata** and **metadata** interfaces of OpenStack and check whether the request is intercepted:

            .. code-block::

               curl 169.254.169.254/openstack/latest/meta_data.json
               curl 169.254.169.254/openstack/latest/user_data
