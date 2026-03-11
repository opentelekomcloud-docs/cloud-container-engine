:original_name: cce_bestpractice_10047.html

.. _cce_bestpractice_10047:

Using Images Securely in a CCE Cluster
======================================

Container images are the primary defense against external attacks and are crucial for securing applications, systems, and the entire supply chain. If an image is insecure, it can become a vulnerability for attackers to exploit. This can lead to the container escaping to its node, allowing attackers to access sensitive data on the node or use it as a launching pad to gain control over the entire cluster or tenant account. This section describes some recommended configurations to mitigate such risks.

Minimizing a Container Image
----------------------------

To improve container image security, remove any unnecessary binary files. When using an unknown image from Docker Hub, you are advised to review the image with a tool like Dive. Dive provides layer-by-layer details of an image, helping to identify potential security risks. For details, see `Dive <https://github.com/wagoodman/dive>`__.

For improved security, it is advised to remove binary files with setuid and setgid permissions because they can be exploited to elevate permissions. It is also wise to remove shell tools and applications that could be used maliciously, like nc and curl. To locate files with setuid and setgid bits, use the following command:

.. code-block::

   find / -perm /6000 -type f -exec ls -ld {} \;

To remove special permissions from the obtained files, add the following command to your container image:

.. code-block::

   RUN find / -xdev -perm /6000 -type f -exec chmod a-s {} \; || true

Using Multi-Stage Builds
------------------------

Multi-stage builds are a great way to create container images efficiently, especially in the CI process. With multi-stage builds, you can perform lint checks on source code or analyze static code during the builds, providing quick feedback to developers. There is no need to wait for the entire build to finish.

Multi-stage builds offer significant security advantages by allowing developers to include only necessary components in container images that will be pushed to container registries, excluding build tools and other unnecessary binary files. This approach reduces the attack surface of images and improves overall security.

For details about the concepts, best practices, and advantages of multi-stage builds, see the `Docker documentation <https://docs.docker.com/develop/develop-images/multistage-build/>`__. This will help you create streamlined, secure container images while optimizing development and deployment processes.

Using SWR
---------

SWR provides easy, secure, reliable management of container images throughout their lifecycles, featuring image push, pull, and deletion.

SWR stands out for its precise permissions management, allowing administrators to customize access permissions for different users with read, edit, and manage levels. This ensures image security and compliance, meeting the needs of team collaboration.

Additionally, SWR offers automatic deployment capabilities. Triggers help you automatically update applications that were created from images with updated tags. After a new image tag is released, SWR automatically triggers the update of the application that was created from the image on CCE, streamlining CI/CD.

To further enhance SWR's security and flexibility, precisely control the IAM user permissions.

Scanning an Image Using SWR
---------------------------

With SWR, you can easily scan and secure your images with just a few clicks. Image scanning provides a thorough security check for your private images in repositories. It detects potential vulnerabilities and offers rectification suggestions.

Using an Image Signature and Configuring a Signature Verification Policy
------------------------------------------------------------------------

Image signature verification is a security measure that confirms whether a container image has been tampered with after its creation. The image creator can sign the image content, and users can verify the image's integrity and source by checking the signature.

This verification is crucial in maintaining container image security. By using image signature verification, organizations can guarantee the security and reliability of their containerized applications and safeguard them from potential security risks.

Adding the USER Instruction to a Dockerfile and Running Commands as a Non-root User
-----------------------------------------------------------------------------------

Configuring proper user permissions during container build and deployment can greatly enhance container security. This not only helps prevent potential malicious activities, but also aligns with the PoLP.

By setting the USER instruction in Dockerfiles, subsequent commands can be executed as non-root users, which is a standard security practice.

-  Limited permissions: Running a container as a non-root user can mitigate potential security risks because attackers cannot gain full control over the node even if the container is attacked.
-  Restricted access: Non-root users typically have limited permissions, which restrict their access to and operations on node resources.

In addition to Dockerfiles, you can use the **securityContext** field in **podSpec** of Kubernetes to configure user and group IDs and enforce security policies during container deployment.
