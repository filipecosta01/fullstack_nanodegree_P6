Udacity - Linux Server Configuration - Project 6 - Filipe Costa
============

This is a manual to describe the whole process of serving a project in a Linux Server machine.

## Linux Server - Microsoft Cloud Azure

In order to test new cloud environments to really learn how to operate them and properly
configure the services for a web application, the Microsoft Azure was used to deploy the Item Catalog project.
Microsoft Azure is really flexible, intuitive tool and powerful. Moreover, it's "free", as they provide a bonus
of $300.00 for the first month of usage, so it's possible to choose an adequated machine for deployment or even
default configurations for web applications, mobile applications and more.
[Read more about Microsoft Azure](https://azure.microsoft.com/en)

### More About the Linux Server

The specs of the virtual machine that is running on the cloud are:
DS1_V2 Standard machine, single core, 7GB SSD.

Microsoft Azure allows the users to configure outbound and inbound security as below:
- Outbound Security Configuration
![For Outbound Security](https://cloud.githubusercontent.com/assets/2905403/26757066/3f6f0ca0-4887-11e7-858e-1b9a9ff99273.png)

- Inbound Security Configuration
![For Inbound Security](https://cloud.githubusercontent.com/assets/2905403/26757067/3f89d6ca-4887-11e7-897b-47b13520fcd9.png)

The virtual machine has separated environments to make even easier to make it more obvious where to find
and configure each property you want (is it related to network? Disk? Backup?).
So, in order to keep it really simple, they made a super intuitive interface and we didn't need to configure
the firewalls using [UFW](https://www.linux.com/learn/introduction-uncomplicated-firewall-ufw).

## Database

Postgresql is currently the database used for the Item Catalog web application.
The default database is `catalog` and the default user, owner of this database is `catalog` too.

To create the database, follow the steps:
1. Start the conection with the server and properly log in
2. Run `sudo -u postgres psql` to start the Postgresql shell
3. Run `CREATEDB catalog` to create the catalog database

To create the user, follow the steps:
1. Start the conection with the server and properly log in
2. Run `sudo -u postgres psql` to start the Postgresql shell
3. Run `CREATE USER catalog WITH PASSWORD '<SOME_PASSWORD>';` to create the catalog user
4. Run `GRANT ALL PRIVILEGES ON DATABASE "catalog" TO catalog;` to grant the permissions to catalog user

## Apache2 Configs

To correctly deploy the application, Apache2 keeps a folder where it's possible to include the page's contents, such as html, javascript and css files.
So, to deploy the application, do as follow:
1. Clone the Item Catalog project and move the main folder to `/var/www/`
2. Create the `myapp.wsgi` (Web Server Gateway Interface) file inside `/var/www/html` as:
```
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)

sys.path.insert(0, "/var/www/item-catalog/")

from server import app as application
application.secret_key = "full_stack_catalog_project"
```
  Where:
  1. The `application.secret_key` can be defined as any string

Then, after these steps, it's time to tell the Apache2 that the server should run this new file `myapp.wsgi`. Do as follow:
1. Already connected to the remote server, go to `/etc/apache2/sites-enabled/`
2. Configure the `000-default.conf` file as:
```
<VirtualHost *:80>
	ServerName 191.232.190.28
	ServerAlias fullstack-filipecosta-p6.brazilsouth.cloudapp.azure.com
	ServerAdmin webmaster@localhost
	DocumentRoot /var/www/html

	ErrorLog ${APACHE_LOG_DIR}/error.log
	LogLevel debug
	CustomLog ${APACHE_LOG_DIR}/access.log combined

        WSGIScriptAlias / /var/www/html/myapp.wsgi
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
```
  Where:
  1. ServerName is the current server name where the app will be hosted
  2. ServerAlias is the url where the app can be accessed
  3. WSGIScriptAlias should have the main page ("/") pointing to the file configured few steps before.
  
Now restart the Apache2 (`sudo service apache2 restart`) and Apache2Ctl (`sudo apache2ctl restart`).

Access the URL in ServerAlias above and the application must be running.

### Where to Find the Application?
You can find the Item Catalog project running on the Linux Server here:
- Complete URL: http://fullstack-filipecosta-p6.brazilsouth.cloudapp.azure.com/

### For the Udacity Grader
You can access the server via ssh using the private key sent in the notes.
To do so, follow as below:
1. Get the whole text in the notes and paste into a new file with no extension
2. Open a new terminal and run the command `sudo ssh grader@191.232.190.28 -p 2200 -i <PATH_TO_PRIVATE_RSA_FILE>`
  1. Where PATH_TO_PRIVATE_RSA_FILE is the file mentioned in step one
3. Terminal should wait for a password to be inputed, this should be `grader`
4. You'll be connected to the Linux Server hosted by Microsoft Azure

Note that:
- The IP address of the server is: 191.232.190.28
- The Port for SSH access is: 2200


## What Changed in the Project, then?

From the terminal, after access the virtual machina through SSH, had to:
1. First, update and upgrade the machine, according to instructions
2. Install, using `sudo apt-get install` command, the applications:
   1. apache2
   2. postgresql
   3. libapache2-mod-wsgi

Then, for the Item Catalog project:

3. Install, using `sudo apt-get install python-` command, the libs:
    1. flask
    2. sqlalchemy
    3. oauth2client
    4. httplib2
    5. requests
    6. psycopg2

4. Changed database_setup.py file to connect to postgresql database and do the initial setup
5. Changed catalog_loader.py file to connect to postgresql database to run the initial data
6. Changed server.py file to:
    1. Connect to postgresql database instead of sqlite one
    2. Get the Google OAuth configuration file according to the canonical path

And that was it. The project is running.

## [Contacting the Author](mailto:s.costa.filipe@gmail.com)
Click above and feel free to get in touch in case of trouble or suggestions.
