:original_name: cce_10_0016.html

.. _cce_10_0016:

Using a Secret
==============

.. important::

   The following secrets are used by the CCE system. Do not perform any operations on them.

   -  Do not operate secrets under kube-system.
   -  Do not operate default-secret and paas.elb in any of the namespaces. The default-secret is used to pull the private image of SWR, and the paas.elb is used to connect the service in the namespace to the ELB service.

-  :ref:`Configuring the Data Volume of a Pod <cce_10_0016__section472505211214>`
-  :ref:`Setting Environment Variables of a Pod <cce_10_0016__section207271352141216>`

The following example shows how to use a secret.

.. code-block::

   apiVersion: v1
   kind: Secret
   metadata:
     name: mysecret
   type: Opaque
   data:
     username: ****** #The value must be Base64-encoded.
     password: ******  #The value must be encoded using Base64.

.. important::

   When a secret is used in a pod, the pod and secret must be in the same cluster and namespace.

.. _cce_10_0016__section472505211214:

Configuring the Data Volume of a Pod
------------------------------------

A secret can be used as a file in a pod. As shown in the following example, the username and password of the **mysecret** secret are saved in the **/etc/foo** directory as files.

.. code-block::

   apiVersion: v1
   kind: Pod
   metadata:
     name: mypod
   spec:
     containers:
     - name: mypod
       image: redis
       volumeMounts:
       - name: foo
         mountPath: "/etc/foo"
         readOnly: true
     volumes:
     - name: foo
       secret:
         secretName: mysecret

In addition, you can specify the directory and permission to access a secret. The username is stored in the **/etc/foo/my-group/my-username** directory of the container.

.. code-block::

   apiVersion: v1
   kind: Pod
   metadata:
     name: mypod
   spec:
     containers:
     - name: mypod
       image: redis
       volumeMounts:
       - name: foo
         mountPath: "/etc/foo"
     volumes:
     - name: foo
       secret:
         secretName: mysecret
         items:
         - key: username
           path: my-group/my-username
           mode: 511

To mount a secret to a data volume, you can also perform operations on the CCE console. When creating a workload, set advanced settings for the container, choose **Data Storage > Local Volume**, click **Add Local Volume**, and select **Secret**. For details, see :ref:`Secret <cce_10_0377__section10197243134710>`.

.. _cce_10_0016__section207271352141216:

Setting Environment Variables of a Pod
--------------------------------------

A secret can be used as an environment variable of a pod. As shown in the following example, the username and password of the **mysecret** secret are defined as an environment variable of the pod.

.. code-block::

   apiVersion: v1
   kind: Pod
   metadata:
     name: secret-env-pod
   spec:
     containers:
     - name: mycontainer
       image: redis
       env:
         - name: SECRET_USERNAME
           valueFrom:
             secretKeyRef:
               name: mysecret
               key: username
         - name: SECRET_PASSWORD
           valueFrom:
             secretKeyRef:
               name: mysecret
               key: password
     restartPolicy: Never
