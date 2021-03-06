docker-mysql
==================

Base docker image to run a MySQL database server.

This is a fork of [`tutum-docker-mysql`](https://github.com/tutumcloud/tutum-docker-mysql)

Usage
-----

To create the image `westhouse/mysql`, execute the following command on the docker-mysql folder:

	docker build -t westhouse/mysql .

To run the image and bind to port 3306:

	docker run -d -p 3306:3306 westhouse/mysql

The first time that you run your container, a new user `admin` with all privileges 
will be created in MySQL with a random password. To get the password, check the logs
of the container by running:

	docker logs <CONTAINER_ID>

You will see an output like the following:

	========================================================================
	You can now connect to this MySQL Server using:

	    mysql -uadmin -p47nnf4FweaKu -h<host> -P<port>

	Please remember to change the above password as soon as possible!
	MySQL user 'root' has no password but only allows local connections
	========================================================================

In this case, `47nnf4FweaKu` is the password allocated to the `admin` user.

Remember that the `root` user has no password but it's only accesible from within the container.

You can now test your deployment:

	mysql -uadmin -p

Done!


Setting a specific password for the admin account
-------------------------------------------------

If you want to use a preset password instead of a random generated one, you can
set the environment variable `MYSQL_PASS` to your specific password when running the container:

	docker run -d -p 3306:3306 -e MYSQL_PASS="mypass" westhouse/mysql

You can now test your deployment:

	mysql -uadmin -p"mypass"


Mounting the database file volume
---------------------------------

After this you can start your mysql image but this time using `/path/in/host` as the database folder:

	docker run -d -p 3306:3306 -v /path/in/host:/var/lib/mysql westhouse/mysql


Migrating an existing MySQL Server
----------------------------------

In order to migrate your current MySQL server, perform the following commands from your current server:

To dump your databases structure:

	mysqldump -u<user> -p --opt -d -B <database name(s)> > /tmp/dbserver_schema.sql

To dump your database data:

	mysqldump -u<user> -p --quick --single-transaction -t -n -B <database name(s)> > /tmp/dbserver_data.sql

To import a SQL backup which is stored for example in the folder `/tmp` in the host, run the following:

	sudo docker run -d -v /tmp:/tmp westhouse/mysql /bin/bash -c "/import_sql.sh <rootpassword> /tmp/<dump.sql>")

Where `<rootpassword>` is the root password set earlier and `<dump.sql>` is the name of the SQL file to be imported.


Environment variables
---------------------

`MYSQL_PASS`: Set a specific password for the admin account.

Compatibliity Issues
--------------------

- Volume created by MySQL 5.6 cannot be used in MySQL 5.5 Images or MariaDB images
