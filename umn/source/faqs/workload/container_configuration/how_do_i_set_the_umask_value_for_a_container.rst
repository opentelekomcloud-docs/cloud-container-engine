:original_name: cce_faq_00230.html

.. _cce_faq_00230:

How Do I Set the umask Value for a Container?
=============================================

Symptom
-------

A container is started in **tailf /dev/null** mode and the directory permission is **700** after the startup script is manually executed. If the container is started by Kubernetes itself without **tailf**, the obtained directory permission is **751**.

Solution
--------

The reason is that the umask values set in the preceding two startup modes are different. Therefore, the permissions on the created directories are different.

The umask value is used to set the default permission for a newly created file or directory. If the umask value is too small, group users or other users will have excessive permissions, posing security threats to the system. Therefore, the default umask value for all users is set to **0077**. That is, the default permission on directories created by users is **700**, and the default permission on files is **600**.

You can add the following content to the startup script to set the permission on the created directory to **700**:

#. 1. Add **umask 0077** to the **/etc/bashrc** file and all files in **/etc/profile.d/**.

#. Run the following command:

   .. code-block::

      echo "umask 0077" >> $FILE

   .. note::

      *FILE* indicates the file name, for example, **echo "umask 0077" >> /etc/bashrc**.

#. Set the owner and group of the **/etc/bashrc** file and all files in **/etc/profile.d/** to **root**.

#. Run the following command:

   .. code-block::

      chown root.root $FILE
