# Project: Linux Server Configuration

Udacity- Full Stack Web Develper Nanodegree by Udacity Eighth Project.

## Server:
###Name of instance: Item-Catalog-Instance
###IP Address: 52.15.255.82 (public_key)
Port: 2200
private_key: Not available (172.26.9.178) 
###URL: ~~ 'http://ec2-52-15-255-82.us-east-2.compute.amazonaws.com' ~~

### Softwares installed

    - Apache2 and modules
    - PostgreSQL and modules
    - Git
    - Pip and virtualenv
# In the local terminal cd /root/.ssh/ folder - ran 
 'ssh-keygen -t rsa' - to generate a public and private keys.
# My identification has been saved in /root/.ssh/grader_key.rsa.
# public key has been saved in /root/.ssh/grader_key.rsa.pub.
## passphrase - grader01
## Delete the firstline in /root/.ssh/known_hosts - otherwise you might have error.

Create new user grader and give it the permission sudo
  - SSH into the server 
   
   `ssh -i ~/.ssh/grader_key root@52.15.255.82`
   
   
# To create a new user named grader
'sudo adduser grader`
password: password

# To give sudo permission to new user grader:
'sudo adduser grader sudo'

# Update all currently installed packages
# downloads the package lists
`sudo apt-get update`

# Fetch new versions of packages
`sudo apt-get upgrade`

# will remove those dependencies that were installed with applications
# and that are no longer used by anything else on the system
'sudo apt-get autoremove'

#
'sudo apt-get install finger'
# To reboot
'sudo /sbin/reboot'

# The finger displays information about the system grader
'sudo finger grader'
Output:
Login: grader         Name: Udacity Grader
Directory: /home/grader             Shell: /bin/bash
Never logged in.
No mail.
No Plan.
   


# Configuration Uncomplicated Firewall (UFW)
By default, block all incoming connections on all ports:

`sudo ufw default deny incoming`

# Allow outgoing connection on all ports:

`sudo ufw default allow outgoing`

'sudo ufw status'

'sudo ufw allow ssh'

# Allow incoming connection for SSH on port 2200:

`sudo ufw allow 2200/tcp`

# Allow incoming connections for HTTP on port 80:

`sudo ufw allow 80/tcp`

# Allow incoming connection for NTP on port 123:

`sudo ufw allow ntp`

# To check the rules that have been added before enabling the firewall use:

`sudo ufw show added`

# To enable the firewall, use:

`sudo ufw enable`

# To check the status of the firewall, use:

`sudo ufw status`
## Output:
To                         Action      From
--                         ------      ----
22                         ALLOW       Anywhere                  
2200/tcp                   ALLOW       Anywhere                  
80/tcp                     ALLOW       Anywhere                  
123                        ALLOW       Anywhere                  
22 (v6)                    ALLOW       Anywhere (v6)             
2200/tcp (v6)              ALLOW       Anywhere (v6)             
80/tcp (v6)                ALLOW       Anywhere (v6)             
123 (v6)                   ALLOW       Anywhere (v6)  

# Create a new file in the sudoers directory:
`sudo nano /etc/sudoers.d/grader`
   
  - Add the following text `grader ALL=(ALL:ALL) ALL`
  - Run `sudo nano /etc/hosts`
  
## Fix `sudo: unable to resolve host` error
When the `grader` user issues a `sudo` command, got the following warning:
`sudo: unable to resolve host ip-xx-xx-xx-xxx`

To fix this, the hostname was added to the loopback address in the 
`cd /etc/hosts` file
so that th first line now reads:
`127.0.0.1 localhost ip-xx-xx-xx-xxx` -- ip is the private ip.



# Change timezone to UTC
# To switch to UTC, simply execute:
'sudo dpkg-reconfigure tzdata',
# scroll to the bottom of the Continents list and select Etc or None of the above;
# in the second list, select UTC. If you prefer GMT instead of UTC, it's just above UTC in that list.




# Set-up SSH keys for user grader on virtual machine:

'sudo mkdir /home/grader/.ssh'
'sudo chown grader:grader /home/grader/.ssh'
'sudo chmod 700 /home/grader/.ssh'
'sudo cp /root/.ssh/authorized_keys /home/grader/.ssh/'
'sudo chown grader:grader /home/grader/.ssh/authorized_keys'
'sudo nano /home/grader/.ssh/authorized_keys'

# Open the .pub file from your local machine and coppied the content into the authorized file.
#Copy the rsa key generated on local machine to vm's authorized file and save
# To copy press Ctrl+Shift+Alt, which will open up a text box. Paste the text into that box. 
# Then press Ctrl+Shift+Alt again to go back to the terminal. 
# Right click to paste the text into the terminal (nano text editor).
# right click will paste the content.

'sudo chmod 644 /home/grader/.ssh/authorized_keys'

# reload SSH using 
'sudo service ssh restart'

# Disable root login
## Change the following line in the file 
`sudo nano /etc/ssh/sshd_config`
From `PermitRootLogin without-password` to `PermitRootLogin no`.
Also, uncomment the following line so it reads:
'''
PasswordAuthentication no

'''

# added SSH port to 2200
# Edit the file 
`sudo nano /etc/ssh/sshd_config`

# added port 2200 with the existing 22 port:
`Port 2200`

# Then restart the SSH service:
`sudo service ssh restart`

# Logged into the server from your local machine as a grader.
$ ssh -i ~/.ssh/grader_key -p 22 grader@52.15.255.82

# It will ask you to enter the passphrase:
# Enter passphrase for key '/root/.ssh/grader': grader01


# Install Apache to serve a Python mod_wsgi application


# Install Apache:
`sudo apt-get install apache2`
## type `52.15.255.82` on URL and can see the apache ubuntu default page.

# Install the `libapache2-mod-wsgi` package:
`sudo apt-get install libapache2-mod-wsgi python-dev`

# Enable mod_wsgi
'sudo a2enmod wsgi'

# You then need to configure Apache to handle requests using the WSGI module.
# Edit the /`etc/apache2/sites-enabled/000-default.conf` file. This file tells Apache 
# how to respond to requests, where to find the files for a particular site and much more.
# add the following line at the end of the <VirtualHost *:80> block, right before the closing </VirtualHost>
# line: `WSGIScriptAlias / /var/www/html/myapp.wsgi`

# Start the web server with 
`sudo service apache2 start`

# First confirm that the WSGI module is actually installed:

'dpkg -l libapache2-mod-wsgi'

# If you get following error:
`sudo nano /etc/apache2/conf-available/fqdn.conf`
* add this line :  `Servername ip-172-26-7-85` 
* `sudo a2enconf fqdn`   and save the file.

OR 

`echo "ServerName localhost" | sudo tee /etc/apache2/conf-available/fqdn.conf && sudo a2enconf fqdn`

##Note: 
To get permission to modify fqdn.conf (only root user and group can modify this file).
Privileged access is required to access that directory.
Use `sudo -i` to gain privileged access. Be sure to exit privileged mode when finished 
performing privileged tasks.

# restart Apache:
`sudo service apache2 restart`
# To test if you have your Apache configuration correct write a WSGI application.
# Create the /var/www/html/myapp.wsgi file using the command `sudo nano /var/www/html/myapp.wsgi`. 
# write the following application:
```
    def application(environ, start_response):
    status = '200 OK'
    output = 'Hello World!'

    response_headers = [('Content-type', 'text/plain'), ('Content-Length', str(len(output)))]
    start_response(status, response_headers)

    return [output]
```
#  This application will simply print return Hello World! along with the required HTTP response headers.
# reload `http://52.15.255.82/` and see the output in the browser.




# Install and configure PostgreSQL
*  Install the Python PostgreSQL adapter psycopg:
        sudo apt-get install python-psycopg2
  - `sudo apt-get install libpq-dev python-dev`
  - `sudo apt-get install postgresql postgresql-contrib`
  `* To check, no remote connections are allowed :
        sudo vim /etc/postgresql/9.5/main/pg_hba.conf looks like this:
  ```
  local   all             postgres                                peer
  local   all             all                                     peer
  host    all             all             127.0.0.1/32            md5
  host    all             all             ::1/128                 md5
  ```
  
  # Login as user "postgres"
  - `sudo su - postgres`
  # Get into postgreSQL shell
  
  - `psql`
  - `CREATE USER catalog WITH PASSWORD 'password';`
  - `ALTER USER catalog CREATEDB;`
  - `CREATE DATABASE catalog WITH OWNER catalog;`
  - `\c catalog`
  - `REVOKE ALL ON SCHEMA public FROM public;`
 # Execute database_setup.py , login as psql and check all the tables with following commands:
	- connect to database using : `\c catalog`
	- To see the tables in schema :` \dt`
	- to see particular table:` \d [tablename]`
	- to see the entries/data in table :`select * from [tablename];`
	- to drop the table:`drop table [tablename];`
  - `GRANT ALL ON SCHEMA public TO catalog;`
  - `\q`
  - `exit`
# restart postgresql: 
`sudo service postgresql restart`

#Restart Apache 
`sudo service apache2 restart`  

# Creating a Flask App
# Clone the Item Catalog Project from Github
  - Install git using: 
  `sudo apt-get install git`
  - `cd /var/www`
  `sudo mkdir catalog`
  - Change owner of the newly created www folder:
  `sudo chown -R grader:grader catalog`
# Clone your project from github 
`git clone https://github.com/sabrinadowla14/Build-an-Item-Catalog-Application.git catalog`
  - `cd /catalog`
### /var/www/catalog/catalog$ ls
### client_secret_value.json  fb_client_secrets.json  itemsinfo.py  templates
### database_setup.py         __init__.py             project.py
### database_setup.pyc        itemsdatabase.db        static

  
  # Inside catalog folder create a static and templates folder
  'sudo mkdir static templates'
  
  # Set new password for grader.
  'sudo passwd grader' -- password
  
 
# create the __init__.py file that will contain the flask application logic.
    `sudo nano __init__.py`
# Add following logic to the file:
```
from flask import Flask
app = Flask(__name__)
@app.route("/")
def hello():
    return "Welcome to Item-Catalog!!"
if __name__ == "__main__":
    app.run()
```


# Edit database_setup.py, __init__.py, project.py and itemsinfo.py and change engine = create_engine('sqlite:///itemsdatabase.db')
# to engine = create_engine('postgresql://catalog:password@localhost/catalog')
  

# Restart Apache 
'sudo service apache2 restart'

# Install virtual environment for flask application inside /var/www/catalog/catalog$.
  - `sudo apt-get install python-pip` 
  - Install the virtual environment 
  - `sudo pip install virtualenv`
  - Create a new virtual environment 
  `sudo virtualenv venv`
  - Activate the virutal environment 
  `source venv/bin/activate`

# Give this command to install Flask inside of (venv):

  'sudo pip install Flask'
# Install pip with 
  `sudo apt-get install python-pip`
  `sudo pip install requests`
# Install other project dependencies 
  `sudo pip install httplib2 oauth2client sqlalchemy psycopg2 sqlalchemy_utils`
  `sudo pip install flask-seasurf`
# Run the following command to test if the installation is successful and the app is running:
  'sudo python __init__.py' 
# It should display “Running on http://localhost:5000/”
# or "Running on http://127.0.0.1:5000/". If you see this message, 
# you have successfully configured the app. 
# To deactivate the environment, give the following command:
'deactivate'

# move the project.py file to __init__.py file :
 `sudo mv project.py __init__.py`
 
# All files and directory inside calalog folder:
:/var/www/catalog/catalog$ ls
client_secret_value.json  fb_client_secrets.json  itemsinfo.py  venv
database_setup.py         __init__.py             static
database_setup.pyc        itemsdatabase.db        templates

 
# Configure and Enable a New Virtual Host 
# Run this: 
`sudo nano /etc/apache2/sites-available/catalog.conf`
- Paste this code: 
  '''
  <VirtualHost *:80>
		ServerName ec2-52-15-255-82.us-east-2.compute.amazonaws.com
		ServerAdmin admin@ec2-52-15-255-82.us-east-2.compute.amazonaws.com
		WSGIScriptAlias / /var/www/catalog/catalog.wsgi
		<Directory /var/www/catalog/catalog/>
			Order allow,deny
			Allow from all
		</Directory>
		Alias /static /var/www/catalog/catalog/static
		<Directory /var/www/catalog/catalog/static/>
			Order allow,deny
			Allow from all
		</Directory>
		ErrorLog ${APACHE_LOG_DIR}/error.log
		LogLevel warn
		CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
  '''

# Disable the default virtual host with:

`sudo a2dissite 000-default.conf`

# Then enable the catalog app virtual host:

`sudo a2ensite catalog.conf` 


##Note:

## If you get following error
```
 apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.0.1. 
 Set the 'ServerName' directive globally to suppress this message
   ...done.
```
 Edit the catalog.conf file by setting your Servername, ServerAdmin as:
```
<VirtualHost *:80>
         ServerName 52.15.255.82
         ServerAdmin admin@52.15.255.82
         ServerAlias ec2-52-15-255-82.us-east-2.compute.amazonaws.com
``` 
# Create the .wsgi File:
'sudo nano /var/www/catalog/catalog.wsgi'
 and add the following inside:
  ```
#!/usr/bin/python
import sys
import logging
logging.basicConfig(stream=sys.stderr)
sys.path.insert(0,"/var/www/catalog/")

from project import app as application
application.secret_key = 'super_secret_key'
  ```
# Restart Apache
'sudo service apache2 restart'
  
# go to 'cd /etc/apache2/sites-enabled'
# Look for symbolic link of catalog.conf file. 

# Run
- `python /var/www/catalog/catalog/database_setup.py`
  - Make sure no remote connections to the database are allowed.
  
  
Now directory structure should look like this:
|---catalog
|-----catalog.wsgi
|-----catalog
|-----------------------static
|-----------------------templates
|-----------------------venv
|-----------------------__init__.py
|



# Edit database_setup.py, project.py and itemsinfo.py and change engine = create_engine('sqlite:///itemsdatabase.db')
# to engine = create_engine('postgresql://catalog:password@localhost/catalog')

# Google OAuth client secrets file - In client_secret_value.json file:
# Google Authorization steps:
  # Go to [console.developer](https://console.developers.google.com/)
  # click on Credentails --> edit
  - add you hostname "http://ec2-52-15-255-82.us-east-2.compute.amazonaws.com" and public IP address "http://52.15.255.82" 
    to Authorised   JavaScript origins.
  - add hostname (http://ec2-52-15-255-82.us-east-2.compute.amazonaws.com/oauth2callback) to Authorised redirect URIs.
  * update the client_secret.json file too(adding hostname and public IP address).
# Change the `javascript_origins` field to the IP address and AWS assigned URL of the host.
# In this instance that would be:
`"javascript_origins":["http://52.15.255.82", "http://ec2-52-15-255-82.us-east-2.compute.amazonaws.com"]`
# Update path of client_secret_value.json file
  - `sudo nano project.py`
  - Change client_secret_value.json path to `/var/www/catalog/catalog/client_secret_value.json`


# Update the Facebook OAuth client secrets file
# In the file `fb_client_secrets.json`, fill in the `app_id` and `app_secret` fields with
# the correct values.

# Facebook Authorization steps:
  # Go to [developer.facebook](https://developers.facebook.com/)
  # open your application and click on Facebook Login --> settings.
  # Add hostname and public IP address to Valid OAuth redirect URIs and save it.
`http://ec2-52-15-255-82.us-east-2.compute.amazonaws.com`.
`http://ec2-52-15-255-82.us-east-2.compute.amazonaws.com/oauthcallback.html`

# Change the path in client_secrets_value.json and fb_client_secrets.json files. 
# give absolute path to these files . change the CLIENT_ID = json.loads( open('client_secrets_value.json', 'r').read())['web']['client_id'] to

open(r'/var/www/catalog/catalog/client_secrets_value.json', 'r').read())['web']['client_id']```
Similarly for `fb_client_secrets.json` file. Similarly change the path for `fb_client_secrets.json` file.
# Apache runs as the user www-data, so it is this user that needs access. 
# In any case, you can set it so that any user can read the file, like this:

'sudo chmod 664 /var/www/catalog/catalog/client_secret_value.json'


# Run the application:
Click on: `http://52.15.255.82` or `http://ec2-52-15-255-82.us-east-2.compute.amazonaws.com`

Alternative way:
- Create the datbase schema:
    `python database_setup.py`
    `python itemsinfo.py`
	`/var/www/catalog/client_secret_value.json`
- Restart Apache : `sudo service apache2 restart`
- in /var/www/catalog/catalog directory : execute -  `python __init__.py`
- type  public IPaddress (`http://52.15.255.82/`) on URL and you will see your Item-Catalog Webpage.

Git hub Link For "Build an Item Catalog Project":
`https://github.com/sabrinadowla14/Build-an-Item-Catalog-Application`

Important Commands:
sudo apt-get purge  apache2 apache2-utils apache2.2-bin
sudo apt-get autoremove
sudo apt-get install  apache2 apache2-utils apache2.2-bin 
then make sure no services are running on port 80 Code:
sudo netstat -l|grep www
To remove everything in a directory use:
rm -r /var/www/catalog/catalog/*
rm: Deleting Files
File deletion is done using the rm (remove) command.
rm filename
sudo /etc/init.d/apache2 restart
$ sudo /etc/init.d/ssh restart or # service sshd restart
# To empty a file
sudo cp /dev/null filename
# what packages have been installed:  `pip freeze`
What you want to do is ask PostgreSQL:
SHOW hba_file;
location for all log files is /var/log and subdirectories. 
Try /var/log/apache/access.log or /var/log/apache2/access.log

To check apache log:
sudo tail /var/log/apache2/error.log
To check the syntax:
apachectl configtest
sudo tail /var/log/apache2/error.log
sudo less /var/log/apache2/error.log 

Important Sources:
Reverse DNS service to get the domain name of your server:
https://remote.12dt.com/

http://flask.pocoo.org/docs/0.12/deploying/mod_wsgi/
https://help.ubuntu.com/community/PostgreSQL
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
https://askubuntu.com/questions/483670/what-causes-ssh-problems-after-rebooting-a-14-04-server
https://httpd.apache.org/docs/2.4/vhosts/
Deploy a Flask Project
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
https://www.digitalocean.com/community/tutorials/how-to-configure-ssh-key-based-authentication-on-a-linux-server
http://www.hcidata.info/host2ip.cgi
https://classroom.udacity.com/nanodegrees/nd004/parts/00413454014/modules/357367901175461/lessons/4340119836/concepts/48189486140923#), [install apache](http://blog.udacity.com/2015/03/step-by-step-guide-install-lamp-linux-apache-mysql-python-ubuntu.html
http://askubuntu.com/questions/59458/error-message-when-i-run-sudo-unable-to-resolve-host-none
https://docs.python.org/2/howto/logging.html#logging-variable-data
