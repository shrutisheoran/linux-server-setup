# Linux Server Configuration

**_URL for the Web App_**
[http://35.200.179.153](http://35.200.179.153)

To login as **_GRADER_** user in your system run
`ssh grader@35.200.179.153 -p 2200 -i ssh/grader_key`
 > The `grader_key` file contains the private key.
---
### Summary of Softwares Installed
1. **_pip_** - `sudo apt-get install python-pip`
2. **_flask_** - `sudo -H pip install flask`
3. **_httplib2_** - `sudo -H pip install httplib2`
4. **_oauth2client_** - `sudo -H pip install oauth2client`
5. **_psycopg2_** - `sudo -H pip install psycopg2`
6. **_sqlalchemy_** - `sudo -H pip install sqlalchemy`
7. **_sqlalchemy_utilities_** - `sudo -H pip install sqlalchemy_utils`
8. **_python dependencies_**  `sudo -H pip install libpq-dev python-dev`
9. **_postgres_** - `sudo -H install postgresql postgresql-contrib`
10. **_Apache_** - `sudo -H install apache2`
11. **_Mod-WSGI_** - `sudo -H install libapache2-mod-wsgi`
12. **_Python virtual enviroment_** - `sudo -H pip install virtualenv venv`

### Summary of Configurations Made
1. **Grant __sudo__ access to __grader__**
	* Create a new file `grader` in `\etc\sudoers.d`
	* Write `grader ALL=(ALL) NOPASSWD:ALL` in the **grader** file.
	* Save the file
2. **Disable remote login of root user**
	* Run `sudo nano /etc/ssh/sshd_config`
	* Set `PermitRootLogin` to `no`
	* Run `sudo service ssh restart`  
3. **Force Key Based Authentication**
	* Run `sudo nano /etc/ssh/sshd_config`
	* Set `PassowordAuthentication` to `no`
	* Set `UsePAM` to `no`
	* Run `sudo service ssh restart`  
4. **Enable Mod-WSGI**
	* `sudo a2enmod wsgi`  
5. **Create Database** 
	* `create user catalog with encrypted password <password>`
	* `create database catalog`
	* `grant all on database catalog to catalog`
	* `\c catalog`
	* `revoke all on schema public from public`
	* `grant all on schema public to catalog`  
6. **Change SSH Port**
	* Edit the `/etc/ssh/sshd_config` file.
	* Change the __Port__ to __2200__.
	* Save the file.
	* To restart ssh or sshd run `sudo service ssh restart` and `sudo service sshd restart`  
7. **Configure Firewall**
	* First of all to block all incoming requests run `sudo ufw default deny incoming`
	* To allow all outgoing requests run `sudo ufw default allow outgoing`
	* Allow port 2200 for ssh requests - `sudo ufw allow 2200/tcp`
	* Allow port 80 for http requests - `sudo ufw allow www`
	* Allow port 123 for ntp requests - `sudo ufw allow ntp`
	* Finally enable firewall - `sudo ufw enable`
	* To check your firewall status - `sudo ufw status`  
8. **Configure WSGI file**
	* Run the command - `sudo nano /var/www/catalog/project.wsgi`
	* Add the following lines of code
	```import sys
	import logging
	logging.basicConfig(stream=sys.stderr)
	sys.path.insert(0,'/var/www/catalog/')  
	from project import app as application```  
9. **Setup Virtual Environment**
	* `cd venv/lib/python2.7`
	* `source venv/bin/activate`
	* `sudo service apache2 restart`
	* `deactivate`  
10. **Edit Apache Configuration File**
	* Run the command - `sudo nano /etc/apache2/sites-available/000-default.conf`
	* Make the following changes and then run `sudo service apache2 restart`
	```<VirtualHost *:80>
        ServerName 35.200.179.153  
        ServerAdmin shruti.sheoran03@gmail.com
        WSGIDaemonProcess project python-path=/var/www/catalog:/var/www/catalog/venv/lib/python2.7/site-packages
        WSGIProcessGroup project
        WSGIScriptAlias / /var/www/catalog/project.wsgi  
        <Directory /var/www/catalog/>
                Order allow,deny
                Allow from all
        </Directory>
        Alias /static /var/www/catalog/static
        <Directory /var/www/catalog/static/>
                Order allow,deny
                Allow from all
        </Directory>
        ErrorLog ${APACHE_LOG_DIR}/error.log
        LogLevel warn
        CustomLog ${APACHE_LOG_DIR}/access.log combined```  

### Third Party Resources
* __Google Cloud's Compute Engine__