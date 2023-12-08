:original_name: cce_bestpractice_0007.html

.. _cce_bestpractice_0007:

Compiling a Startup Script
==========================

During application containerization, prepare a startup script. The method of compiling this script is the same as that of compiling a shell script. The startup script is used to:

-  Start up the software on which the application depends.
-  Set the configurations that need to be changed as the environment variables.

.. note::

   Startup scripts vary according to applications. Edit the script based on your service requirements.

Procedure
---------

#. Log in as user **root** to the device running Docker.

#. Run the following commands to create the directory where the application is to be stored:

   **mkdir apptest**

   **cd apptest**

#. .. _cce_bestpractice_0007__li12314175319811:

   Compile a script file. The name and content of the script file vary according to applications. Edit the script file based on your application. The following example is only for your reference.

   **vi** **start_tomcat_and_mongo.sh**

   .. code-block::

      #!/bin/bash
      # Load system environment variables.
      source  /etc/profile
      # Start MongoDB. The data is stored in /usr/local/mongodb/data.
      ./usr/local/mongodb/bin/mongod --dbpath=/usr/local/mongodb/data --logpath=/usr/local/mongodb/logs --port=27017 -fork
      # These three script commands indicate that the contents related to the MySQL database in the environment variables are written into the configuration file when Docker is started.
      sed -i "s|mysql://.*/awcp_crmtile|mysql://$MYSQL_URL/$MYSQL_DB|g" /root/apache-tomcat-7.0.82/webapps/awcp/WEB-INF/classes/conf/jdbc.properties
      sed -i "s|username=.*|username=$MYSQL_USER|g" /root/apache-tomcat-7.0.82/webapps/awcp/WEB-INF/classes/conf/jdbc.properties
      sed -i "s|password=.*|password=$MYSQL_PASSWORD|g" /root/apache-tomcat-7.0.82/webapps/awcp/WEB-INF/classes/conf/jdbc.properties
      # Start Tomcat.
      bash /root/apache-tomcat-7.0.82/bin/catalina.sh run
