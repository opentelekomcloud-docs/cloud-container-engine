:original_name: cce_bestpractice_10047.html

.. _cce_bestpractice_10047:

Configuration Suggestions on CCE Container Image Security
=========================================================

Container images are the primary defense against external attacks and are crucial for securing applications, systems, and the entire supply chain. If an image is insecure, it can become a vulnerability for attackers to exploit. This can lead to the container escaping to its node, allowing attackers to access sensitive data on the node or use it as a launching pad to gain control over the entire cluster or tenant account. This section describes some recommended configurations to mitigate such risks.

Minimizing a Container Image
----------------------------

To improve container image security, it is recommended that you remove any unnecessary binary files. When using an unknown image from Docker Hub, you are advised to review the image content with a tool like Dive. Dive provides layer-by-layer details of an image, helping to identify potential security risks. For details, see `Dive <https://github.com/wagoodman/dive>`__.

For improved security, it is recommended that you delete binary files with setuid and setgid permissions, because these can be exploited to elevate permissions. It is also wise to remove shell tools and applications that could be used maliciously, like nc and curl. To locate files with setuid and setgid bits, use the following command:

.. code-block::

   find / -perm /6000 -type f -exec ls -ld {} \;

To remove special permissions from the obtained files, add the following command to your container image:

.. code-block::

   RUN find / -xdev -perm /6000 -type f -exec chmod a-s {} \; || true

Using Multi-Stage Builds
------------------------

Multi-stage builds are a great way to create container images efficiently, especially in the CI process. With multi-stage builds, you can perform lint checks on source code or static code analysis during the build process, providing quick feedback to developers. There is no need to wait for the entire build to finish.

Multi-stage builds offer significant security advantages by allowing developers to include only necessary components in container images, excluding build tools and other unnecessary binary files. This approach reduces the attack surface of images and improves overall security.

For more information about the concepts, best practices, and advantages of multi-stage builds, see the `Docker documentation <https://docs.docker.com/develop/develop-images/multistage-build/>`__. This will help you create streamlined and secure container images while optimizing development and deployment processes.

Using SWR
---------

SWR provides easy, secure, reliable management of container images throughout their lifecycles, featuring image push, pull, and deletion.

SWR stands out for its precise permissions management, allowing administrators to customize access permissions for different users with read, edit, and manage levels. This ensures image security and compliance, meeting the needs of team collaboration.

Additionally, SWR offers automatic deployment capabilities. You can set a trigger to automatically deploy updated image versions. When a new image version is released, SWR automatically triggers the application that uses the image in CCE to update it, streamlining CI/CD.

To further enhance SWR's security and flexibility, fine-grained permissions control can be added to IAM users.

Scanning an Image Using SWR
---------------------------

With SWR, you can easily scan and secure your images with just a few clicks. Image scanning provides a thorough security check for your private images in repositories. It detects potential vulnerabilities and offers rectification suggestions.

Using an Image Signature and Configuring a Signature Verification Policy
------------------------------------------------------------------------

Image signature verification is a security measure that confirms whether a container image has been tampered with after its creation. The image creator can sign the image content, and a user can verify the image's integrity and source by checking the signature.

This verification is crucial in maintaining container image security. By using image signature verification, organizations can guarantee the security and reliability of their containerized applications and safeguard them from potential security risks.

Adding the USER Instruction to a Dockerfile to Run Commands as a Non-root User
------------------------------------------------------------------------------

Properly configuring user permissions during container build and deployment can greatly enhance container security. This not only helps prevent potential malicious activities, but also aligns with the principle of least privilege (PoLP).

By setting the USER instruction in Dockerfiles, subsequent commands are executed as non-root users, which is a standard security practice.

-  Limited permissions: Running a container as a non-root user can also mitigate potential security risks, because attackers cannot gain full control over the node even if the container is attacked.
-  Restricted access: Non-root users typically have limited permissions, which restrict their access to and operation capabilities on node resources.

In addition to Dockerfiles, the **securityContext** field in **podSpec** of Kubernetes can be used to configure user and group IDs and enforce security policies during container deployment.
