linux commands:
to go to previous folder: cd ..
to come back to original folder: cd
Name of instance: Ubuntu-instance-catalog
IP: 54.202.41.238 (public_key)
private_key: 172.26.12.126
DNS: ec2.54.202.41.238.compute-1.amazonaws.com

To add new user grader:
sudo useradd -m -s /bin/bash grader

# To give sudo permission to new user grader:
sudo adduser grader sudo

$ finger grader


## Update all currently installed packages

`sudo apt-get update` - to update the package indexes

`sudo apt-get upgrade` - to actually upgrade the installed packages
sudo apt-get autoremove
sudo apt-get install finger
## To reboot

# /sbin/reboot



## Configuration Uncomplicated Firewall (UFW)
By default, block all incoming connections on all ports:

`sudo ufw default deny incoming`

Allow outgoing connection on all ports:

`sudo ufw default allow outgoing`

$ sudo ufw status

$ sudo ufw allow ssh

Allow incoming connection for SSH on port 2200:

`sudo ufw allow 2200/tcp`

Allow incoming connections for HTTP on port 80:

`sudo ufw allow 80/tcp`

Allow incoming connection for NTP on port 123:

`sudo ufw allow ntp`

To check the rules that have been added before enabling the firewall use:

`sudo ufw show added`

To enable the firewall, use:

`sudo ufw enable`

To check the status of the firewall, use:

`sudo ufw status`

In the local terminal I ran  # $ssh-keygen -t rsa
to generate a public and private keys.
Open the .pub file and coppied the content into the authorized file.
To copy press Ctrl+Shift+Alt, which will open up a text box. Paste the text into that box. 
Then press Ctrl+Shift+Alt again to go back to the terminal. 
Right click to paste the text into the terminal (nano text editor).
right click will paste the content.

My identification has been saved in /root/.ssh/grader.rsa.

Your public key has been saved in /root/.ssh/grader.rsa.pub.


## Set-up SSH keys for user grader

sudo mkdir /home/grader/.ssh
sudo chown grader:grader /home/grader/.ssh
sudo chmod 700 /home/grader/.ssh
sudo cp /root/.ssh/authorized_keys /home/grader/.ssh/
sudo chown grader:grader /home/grader/.ssh/authorized_keys
sudo nano /home/grader/.ssh/authorized_keys
sudo chmod 644 /home/grader/.ssh/authorized_keys



## Disable root login
Change the following line in the file `/etc/ssh/sshd_config`:

From `PermitRootLogin without-password` to `PermitRootLogin no`.

Also, uncomment the following line so it reads:
```
PasswordAuthentication no

```
## Change timezone to UTC
To switch to UTC, simply execute sudo dpkg-reconfigure tzdata,
scroll to the bottom of the Continents list and select Etc or None of the above;
in the second list, select UTC. If you prefer GMT instead of UTC, it's just above UTC in that list.


## added SSH port to 2200
Edit the file `/etc/ssh/sshd_config` added port 2200 with the existing 22 port:

`Port 2200`

Then restart the SSH service:

`sudo service ssh restart`

issue: Not able user grader.

Checked with telnet in the server: 
$ telnet

Will now need to use the following command to login to the server:

`ssh -i ~/.ssh/grader_rsa_key grader@54.202.41.238 -p 2200` This one is not working - saying connection timed out.
# Logged into the server as a grader.
$ ssh -i ~/.ssh/grader_rsa_key -p 22 grader@54.202.41.238   -- This one is working.

## Install Apache to serve a Python mod_wsgi application
Install Apache:

`sudo apt-get install apache2`

Install the `libapache2-mod-wsgi` package:

`sudo apt-get install libapache2-mod-wsgi`


  - Start the web server with `sudo service apache2 start`

  
  

First confirm that the WSGI module is actually installed:

dpkg -l libapache2-mod-wsgi

How to Install and Log Into PostgreSQL on Ubuntu

# Install and configure PostgreSQL
'sudo apt-get remove libpq5'
'sudo apt-get install libpq-dev'
'sudo apt-get install postgresql postgresql-contrib'


Create a PostgreSQL user called `catalog` with:

`sudo -u postgres createuser -P catalog`

You are prompted for a password. This creates a normal user that can't create
databases, roles (users).

Create an empty database called `catalog` with:

`sudo -u postgres createdb -O catalog catalog`


Clone the Item Catalog Project from Github
  - Install git using: 
  `sudo apt-get install git`
  - `cd /deployCatalog`
  - `sudo mkdir item-catalog`
  - Change owner of the newly created item-catalog folder `sudo chown -R grader:grader item-catalog`
  Clone your project from github `git clone https://github.com/sabrinadowla14/Build-an-Item-Catalog-Application.git item-catalog`
  - `cd /item-catalog`
  - Create a item-catalog.wsgi file, then add this inside:

Create the .wsgi File in /deployCatalog/item-catalog/item-catalog.wsgi:
Using the command sudo nano /deployCatalog/item-catalog/catalog/item-catalog.wsgi
Add the following lines of code to the catalog.wsgi file:


#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/deployCatalog/item-catalog/catalog")

from catalog import app as application
application.secret_key = 'super_secret_key'
Rename project.py to __init__.py 
`sudo mv project.py __init__.py`
Edit database_setup.py, project.py and itemsinfo.py and change engine = create_engine('sqlite:///itemsdatabase.db')
to engine = create_engine('postgresql://catalog:password@localhost/catalog')

Restart Apache sudo service apache2 restart

# Google OAuth client secrets file - In client_secret_value.json file:
Change the `javascript_origins` field to the IP address and AWS assigned URL of the host.
In this instance that would be:
`"javascript_origins":["http://54.202.41.238", "http://ec2.54-202-41-238.compute-1.amazonaws.com"]`

These addresses also need to be entered into the Google Developers Console -> API Manager
-> Credentials, in the web client under "Authorized JavaScript origins".

## Update the Facebook OAuth client secrets file
In the file `fb_client_secrets.json`, fill in the `app_id` and `app_secret` fields with
the correct values.

In the Facebook developers website, on the Settings page, the website URL needs to read
`http://ec2.54-202-41-238.compute-1.amazonaws.com`. 


## Configure Apache2 to serve the app
To serve the catalog app using the Apache web server, a virtual host configuration file
needs to be created in the directory `/etc/apache2/sites-available/`, in this case called
`catalog-app.conf`. Here are its contents:

```
<VirtualHost *:80>
        # The ServerName directive sets the request scheme, hostname and port that
        # the server uses to identify itself. This is used when creating
        # redirection URLs. In the context of virtual hosts, the ServerName
        # specifies what hostname must appear in the request's Host: header to
        # match this virtual host. For the default virtual host (this file) this
        # value is not decisive as it is used as a last resort host regardless.
        # However, you must set it for any further virtual host explicitly.
        #ServerName www.example.com

        ServerAdmin webmaster@localhost

        # Define WSGI parameters. The daemon process runs as the www-data user.
        WSGIDaemonProcess catalog user=www-data group=www-data threads=5
        WSGIProcessGroup catalog
        WSGIApplicationGroup %{GLOBAL}

        # Define the location of the app's WSGI file
        WSGIScriptAlias / /deployCatalog/item-catalog/catalog/item-catalog.wsgi

        # Allow Apache to serve the WSGI app from the catalog app directory
        <Directory /deployCatalog/item-catalog/catalog/>
                Require all granted
        </Directory>

        # Setup the static directory (contains CSS, Javascript, etc.)
        Alias /static /deployCatalog/item-catalog/catalog/static

        # Allow Apache to serve the files from the static directory
        <Directory  /deployCatalog/item-catalog/catalog/static/>
                Require all granted
        </Directory>

        # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
        # error, crit, alert, emerg.
        # It is also possible to configure the loglevel for particular
        # modules, e.g.
        #LogLevel info ssl:warn

        ErrorLog ${APACHE_LOG_DIR}/error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

Disable the default virtual host with:

`sudo a2dissite 000-default.conf`

Then enable the catalog app virtual host:

`sudo a2ensite catalog-app.conf`

To make these Apache2 configuration changes live, reload Apache:

`sudo service apache2 reload`


Install Flask and other dependencies
  - Install pip with `sudo apt-get install python-pip`
  - Install Flask `pip install Flask`
  - Install other project dependencies `sudo pip install httplib2 oauth2client sqlalchemy psycopg2 sqlalchemy_utils`
  sudo apt-get install python-psycopg2 python-flask
  sudo pip install oauth2client
  sudo pip install requests
  sudo pip install httplib2
  sudo pip install flask-seasurf

 Install virtual environment
  `sudo pip install virtualenv`
    Create a new virtual environment with
  `sudo virtualenv venv`
  - Activate the virutal environment 
  `source venv/bin/activate`
  - Change permissions 
  `sudo chmod -R 777 venv`
  
  Update path of client_secrets.json file
  - `nano __init__.py`
  - Change client_secrets.json path to `/deployCatalog/item-catalog/catalog/client_secret_value.json`


 Issue:

sudo apt-get purge  apache2 apache2-utils apache2.2-bin
sudo apt-get autoremove
sudo apt-get install  apache2 apache2-utils apache2.2-bin 

then make sure no services are running on port 80 Code:
sudo netstat -l|grep www

Questions:
1. In item catalog project I have used port 5010 because I was not able to use port 8000.
In Linux Configuration Server I have used port 80 in ufw. Do I have to use port 50 in firewall?
2. Not sure if I have created the DNS zone correctly.
3. Do I have to Install virtual environment?
4. For facebook do I have to update anything else? 
5. In client_secret_value.json file do I have to add anything else?

