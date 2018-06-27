---
layout: post
title: "Python WSGI"
date: 2017-01-17 09:09:06 -0700
comments: false
---
How to run a python pyramid application under apache2(httpd) using the mod_wgsi.

Create application user 

```
  useradd appdeploy
```

Edit /etc/passwd

```
  appdeploy:x:<uid>:<gid>:appdeploy:/<home>/<directory>:/sbin/nologin
```

Create an apache2 config file under /etc/httpd/conf.d/

```
<virtuallHost *:80>
  ServerName archive.math.ksu.edu

# Use only 1 Python sub-interpreter.  Multiple sub-interpreters
# play badly with C extensions.  See
# http://stackoverflow.com/a/10558360/209039
WSGIApplicationGroup %{GLOBAL}
WSGIPassAuthorization On
WSGIDaemonProcess pyramid user=appdeploy    group=appdeploy  threads=4 \
   python-path=/var/www/archive/temp_py/lib/python2.7/site-packages
WSGIScriptAlias / /var/www/archive/pyramid.wsgi

<Directory /var/www/archive>
  WSGIProcessGroup pyramid
  Order allow,deny
  Allow from all
</Directory>

</VirtualHost>
```

Create a WSGI file in the application directory that will be called by the apache2 service.

```
import os,sys,site

os.environ['PYTHON_EGG_CACHE'] = '/var/www/archive/temp_py'

from pyramid.paster import get_app, setup_logging
ini_path = '/var/www/archive/production.ini'
#setup_logging(ini_path)
application = get_app(ini_path, 'main')
```

