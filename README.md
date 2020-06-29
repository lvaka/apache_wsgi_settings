# Apache WSGI Settings
General settings for my Apache and WSGI settings.  This is for my Flask apps, but should be able to be used for other apps as well.
 
## WSGI File 
I just name this app.wsgi and put it in the root directory of my project.  This can vary as DJANGO has its WSGI file in the main project folder ie(Project / Project).  Django calls their WSGI file wsgi.py.  So I assume it really doesn't matter what you name it as long as the code is valid.  WSGI files are used to run your Python app in a webserver.  You can use it to load a virtual environment of Python so you can have multiple Python WSGI apps running on the same webserver with different libraries/versions of modules/versions of python 
```
""" 
WSGI App for Flask app.wsgi in root. 
 
    This WSGI app loads the virtual environment before loading the app as an application.  
    If you set this up in Apache or NGINX/gunicorn instead, you can skip everything until 
    the last line.
""" 
import os 
import sys 

app_path = 'exact system path to app root. ie /var/www/wsgi'
virtualenv_name = 'name of your vitualenv, I usually just name mine env'
env_activate = os.path.join(app_path, virtualenv_name, 'bin', 'activate_this.py')
 
with open(env_activate) as file:
    exec(file_.read(), dict(__file__=env_activate)) 
 
sys.path.insert(0, app_path)
 
from appname import app as application 
 
``` 
 
## Apache *.conf File 
This is my Apache's settings for the wsgi app. Just replace app with the name of your app.  Be sure to have all the necessary apache mods to run a WSGI app.  You may need to install additional apps like mysql-client to get Python's MYSQL libraries working.  Be sure to install all required mods necessary for your app.
``` 
# I've seen other guides put the DaemonProcess and ProcessGroup within the VirtualHost tags, but it seems to cause errors for me if I try running the same settings under non-SSL and SSL ports simultaneously. 
WSGIDaemonProcess app threads=5
WSGIProcessGroup app 
<VirtualHost *:80> 
    # The ServerName directive sets the request scheme, hostname and port that 
    # the server uses to identify itself. This is used when creating 
    # redirection URLs. In the context of virtual hosts, the ServerName 
    # specifies what hostname must appear in the request's Host: header to 
    # match this virtual host. For the default virtual host (this file) this 
    # value is not decisive as it is used as a last resort host regardless. 
    # However, you must set it for any further virtual host explicitly. 
    ServerName url.for.your.app #ie coolapp.com or app.cool.com 
 
 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/app #this should be the exact system path to your app's root 
 
    WSGIScriptAlias / /var/www/app/app.wsgi #This is the eact system path to your wsgi file. 
    Alias "/static" "/var/www/app/public/static" #This is to serve static files from your app's directory 
 
    # This is for newer Apache servers.  Older ones may need to use Order Allow, Deny... 
    # or some other config.  Make sure you have your rewrite mod enabled.  'sudo a2enmod rewrite'
    <Directory /var/www/app>
        Require all granted 
    </Directory>


    # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
    # error, crit, alert, emerg.
    # It is also possible to configure the loglevel for particular
    # modules, e.g.
    #LogLevel info ssl:warn

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    # For most configuration files from conf-available/, which are
    # enabled or disabled at a global level, it is possible to
    # include a line for only one particular virtual host. For example the
    # following line enables the CGI configuration for this host only
    # after it has been globally disabled with "a2disconf".
    #Include conf-available/serve-cgi-bin.conf
</VirtualHost>
```
