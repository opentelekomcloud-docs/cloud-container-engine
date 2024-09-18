:original_name: cce_faq_00120.html

.. _cce_faq_00120:

What Should I Do If a Cluster Is Available But Some Nodes Are Unavailable?
==========================================================================

If the cluster status is available but some nodes in the cluster are unavailable, perform the following operations to rectify the fault:

Mechanism for Detecting Node Unavailability
-------------------------------------------

Kubernetes provides the heartbeat mechanism to help you determine node availability. For details about the mechanism and interval, see `Heartbeats <https://kubernetes.io/docs/concepts/architecture/nodes/#heartbeats>`__.

Troubleshooting Process
-----------------------

The issues here are described in order of how likely they are to occur.

Check these causes one by one until you find the cause of the fault.

-  :ref:`Check Item 1: Whether the Node Is Overloaded <cce_faq_00120__section745921416917>`
-  :ref:`Check Item 2: Whether the ECS Is Deleted or Faulty <cce_faq_00120__section19793128323>`
-  :ref:`Check Item 3: Whether You Can Log In to the ECS <cce_faq_00120__section13620173173419>`
-  :ref:`Check Item 4: Whether the Security Group Is Modified <cce_faq_00120__section39419166416>`
-  :ref:`Check Item 5: Whether the Security Group Rules Contain the Security Group Policy for the Communication Between the Master Node and the Worker Node <cce_faq_00120__section64919515161>`
-  :ref:`Check Item 6: Whether the Disk Is Abnormal <cce_faq_00120__section165209286116>`
-  :ref:`Check Item 7: Whether Internal Components Are Normal <cce_faq_00120__section89551837167>`
-  :ref:`Check Item 8: Whether the DNS Address Is Correct <cce_faq_00120__section512785418403>`
-  :ref:`Check Item 9: Whether the vdb Disk on the Node Is Deleted <cce_faq_00120__section1903800103>`
-  :ref:`Check Item 10: Whether the Docker Service Is Normal <cce_faq_00120__section76261312361>`

.. _cce_faq_00120__section745921416917:

Check Item 1: Whether the Node Is Overloaded
--------------------------------------------

**Symptom**

The node connection in the cluster is abnormal. Multiple nodes report write errors, but services are not affected.

**Fault Locating**

#. Log in to the CCE console and click the cluster name to access the cluster console. In the navigation pane, choose **Nodes** and click the **Nodes** tab. Locate the row that contains the unavailable node and click **Monitor**.

#. On the top of the displayed page, click **View More** to go to the AOM console and view historical monitoring records.

   A too high CPU or memory usage of the node will result in a high network latency or trigger system OOM. Therefore, the node is displayed as unavailable.

**Solution**

#. You are advised to migrate services to reduce the workloads on the node and set the resource upper limit for the workloads.
#. Clear data on the CCE nodes in the cluster.
#. Limit the CPU and memory quotas of each container.
#. Add more nodes to the cluster.
#. You can also restart the node on the ECS console.
#. Add nodes to deploy memory-intensive containers separately.
#. Reset the node.

After the node becomes available, the workload is restored.

.. _cce_faq_00120__section19793128323:

Check Item 2: Whether the ECS Is Deleted or Faulty
--------------------------------------------------

#. Check whether the cluster is available.

   Log in to the CCE console and check whether the cluster is available.

   -  If the cluster is unavailable, for example, an error occurs, perform operations described in :ref:`How Do I Locate the Fault When a Cluster Is Unavailable? <cce_faq_00039>`
   -  If the cluster is running but some nodes in the cluster are unavailable, go to :ref:`2 <cce_faq_00120__li20888175614212>`.

#. .. _cce_faq_00120__li20888175614212:

   Log in to the ECS console and view the ECS status.

   -  If the ECS status is **Deleted**, go back to the CCE console, delete the corresponding node from the node list of the cluster, and then create another one.
   -  If the ECS status is **Stopped** or **Frozen**, restore the ECS first. It takes about 3 minutes to restore the ECS.
   -  If the ECS is **Faulty**, restart the ECS to rectify the fault.
   -  If the ECS status is **Running**, log in to the ECS to locate the fault according to :ref:`Check Item 7: Whether Internal Components Are Normal <cce_faq_00120__section89551837167>`.

.. _cce_faq_00120__section13620173173419:

Check Item 3: Whether You Can Log In to the ECS
-----------------------------------------------

#. Log in to the ECS console.

#. Check whether the node name displayed on the page is the same as that on the VM and whether the password or key can be used to log in to the node.

   If the node names are inconsistent and the password and key cannot be used to log in to the node, Cloud-Init problems occurred when an ECS was created. In this case, restart the node and submit a service ticket to the ECS personnel to locate the root cause.

.. _cce_faq_00120__section39419166416:

Check Item 4: Whether the Security Group Is Modified
----------------------------------------------------

Log in to the VPC console. In the navigation pane, choose **Access Control** > **Security Groups** and locate the security group of the cluster master node.

The name of this security group is in the format of *Cluster name*-cce-**control**\ ``-``\ *ID*. You can search for the security group by cluster name and **-cce-control-**.

Check whether the security group rules have been modified. For details about security groups, see :ref:`How Can I Configure a Security Group Rule in a Cluster? <cce_faq_00265>`

.. _cce_faq_00120__section64919515161:

Check Item 5: Whether the Security Group Rules Contain the Security Group Policy for the Communication Between the Master Node and the Worker Node
--------------------------------------------------------------------------------------------------------------------------------------------------

Check whether such a security group policy exists.

When a node is added to an existing cluster, if an extended CIDR block is added to the VPC corresponding to the subnet and the subnet is an extended CIDR block, you need to add the following three security group rules to the master node security group (the group name is in the format of **Cluster name-cce-control-Random number**). These rules ensure that the nodes added to the cluster are available. (This step is not required if an extended CIDR block has been added to the VPC during cluster creation.)

For details about security groups, see :ref:`How Can I Configure a Security Group Rule in a Cluster? <cce_faq_00265>`.

.. _cce_faq_00120__section165209286116:

Check Item 6: Whether the Disk Is Abnormal
------------------------------------------

A 100 GiB data disk dedicated for Docker is attached to the new node. If the data disk is uninstalled or damaged, the Docker service becomes abnormal and the node becomes unavailable.

Click the node name to check whether the data disk mounted to the node is uninstalled. If the disk is uninstalled, mount a data disk to the node again and restart the node. Then the node can be recovered.

.. _cce_faq_00120__section89551837167:

Check Item 7: Whether Internal Components Are Normal
----------------------------------------------------

#. Log in to the ECS where the unavailable node is located.

#. Run the following command to check whether the PaaS components are normal:

   systemctl status kubelet

   If the command is successfully executed, the status of each component is displayed as **active**, as shown in the following figure.

   |image1|

   If the component status is not **active**, run the following commands (using the faulty component **canal** as an example):

   Run **systemctl restart canal** to restart the component.

   After restarting the component, run **systemctl status canal** to check the status.

#. If the restart command fails to be run, run the following command to check the running status of the monitrc process:

   **ps -ef \| grep monitrc**

   If the monitrc process exists, run the following command to kill this process. The monitrc process will be automatically restarted after it is killed.

   **kill -s 9 \`ps -ef \| grep monitrc \| grep -v grep \| awk '{print $2}'\`**

.. _cce_faq_00120__section512785418403:

Check Item 8: Whether the DNS Address Is Correct
------------------------------------------------

#. After logging in to the node, check whether any domain name resolution failure is recorded in the **/var/log/cloud-init-output.log** file.

   **cat /var/log/cloud-init-output.log \| grep resolv**

   If the command output contains the following information, the domain name cannot be resolved:

   Could not resolve host: Unknown error

#. On the node, ping the domain name that cannot be resolved in the previous step to check whether the domain name can be resolved on the node.

   -  If not, the DNS cannot resolve the IP address. Check whether the DNS address in the **/etc/resolv.conf** file is the same as that configured on the VPC subnet. In most cases, the DNS address in the file is incorrectly configured. As a result, the domain name cannot be resolved. Correct the DNS configuration of the VPC subnet and reset the node.
   -  If yes, the DNS address configuration is correct. Check whether there are other faults.

.. _cce_faq_00120__section1903800103:

Check Item 9: Whether the vdb Disk on the Node Is Deleted
---------------------------------------------------------

If the vdb disk on a node is deleted, you can refer to :ref:`this topic <cce_faq_00263>` to restore the node.

.. _cce_faq_00120__section76261312361:

Check Item 10: Whether the Docker Service Is Normal
---------------------------------------------------

#. Run the following command to check whether the Docker service is running:

   .. code-block::

      systemctl status docker

   |image2|

   If the command fails or the Docker service status is not active, locate the cause or contact technical support if necessary.

#. Run the following command to check the number of containers on the node:

   .. code-block::

      docker ps -a | wc -l

   If the command is suspended, the command execution takes a long time, or there are more than 1000 abnormal containers, check whether workloads are repeatedly created and deleted. If a large number of containers are frequently created and deleted, a large number of abnormal containers may occur and cannot be cleared in a timely manner.

   In this case, stop repeated creation and deletion of the workload or use more nodes to share the workload. Generally, the nodes will be restored after a period of time. If necessary, run the **docker rm** {*container_id*} command to manually clear abnormal containers.

.. |image1| image:: /_static/images/en-us_image_0000001981275677.png
.. |image2| image:: /_static/images/en-us_image_0000001950316192.png
