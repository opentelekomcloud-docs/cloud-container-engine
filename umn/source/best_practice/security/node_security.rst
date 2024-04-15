:original_name: cce_bestpractice_0318.html

.. _cce_bestpractice_0318:

Node Security
=============

Preventing Nodes from Being Exposed to Public Networks
------------------------------------------------------

-  Do not bind an EIP to a node unless necessary to reduce the attack surface.
-  If an EIP must be used, properly configure the firewall or security group rules to restrict access of unnecessary ports and IP addresses.

You may have configured the **kubeconfig.json** file on a node in your cluster. kubectl can use the certificate and private key in this file to control the entire cluster. You are advised to delete unnecessary files from the **/root/.kube** directory on the node to prevent malicious use.

rm -rf /root/.kube

Hardening VPC Security Group Rules
----------------------------------

CCE is a universal container platform. Its default security group rules apply to common scenarios. Based on security requirements, you can harden the security group rules set for CCE clusters on the **Security Groups** page of **Network Console**.

Hardening Nodes on Demand
-------------------------

CCE cluster nodes use the default settings of open source OSs. After a node is created, you need to perform security hardening according to your service requirements.

In CCE, you can perform hardening as follows:

-  Use the post-installation script after the node is created. For details, see the description about **Post-installation Script** in **Advanced Settings** when creating a node. This script is user-defined.

Forbidding Containers to Obtain Host Machine Metadata
-----------------------------------------------------

If a single CCE cluster is shared by multiple users to deploy containers, containers cannot access the management address (169.254.169.254) of OpenStack, preventing containers from obtaining metadata of host machines.

For details about how to restore the metadata, see the "Notes" section in `Obtaining Metadata <https://docs.otc.t-systems.com/en-us/usermanual/ecs/en-us_topic_0042400609.html>`__.

.. warning::

   This solution may affect the password change on the ECS console. Therefore, you must verify the solution before rectifying the fault.

#. Obtain the network model and container CIDR of the cluster.

   On the **Clusters** page of the CCE console, view the network model and container CIDR of the cluster.

#. Prevent the container from obtaining host metadata.

   -  VPC network

      a. Log in to each node in the cluster as user **root** and run the following command:

         .. code-block::

            iptables -I OUTPUT -s {container_cidr} -d 169.254.169.254 -j REJECT

         *{container_cidr}* indicates the container CIDR of the cluster, for example, **10.0.0.0/16**.

         To ensure configuration persistence, write the command to the **/etc/rc.local** script.

      b. Run the following commands in the container to access the **userdata** and **metadata** interfaces of OpenStack and check whether the request is intercepted:

         .. code-block::

            curl 169.254.169.254/openstack/latest/meta_data.json
            curl 169.254.169.254/openstack/latest/user_data

   -  Container tunnel network

      a. Log in to each node in the cluster as user **root** and run the following command:

         .. code-block::

            iptables -I FORWARD -s {container_cidr} -d 169.254.169.254 -j REJECT

         *{container_cidr}* indicates the container CIDR of the cluster, for example, **10.0.0.0/16**.

         To ensure configuration persistence, write the command to the **/etc/rc.local** script.

      b. Run the following commands in the container to access the **userdata** and **metadata** interfaces of OpenStack and check whether the request is intercepted:

         .. code-block::

            curl 169.254.169.254/openstack/latest/meta_data.json
            curl 169.254.169.254/openstack/latest/user_data

   -  CCE Turbo cluster

      No additional configuration is required.
