# UQ LRS 

#### Installed on CentOS 6.4 (minimal) with Python 2.6. MySQL database used; please visit https://github.com/adlnet/ADL_LRS for PostgreSQL instuctions.

## Installation

Software Installation

    sudo yum install python-devel gcc python-setuptools
    sudo yum install libxml2 libxml2-devel libxml2-python python-lxml
    sudo yum install libbxslt libxslt-devel libxslt-python
    sudo yum install mysql, mysql-devel,  mysql-server, MySQL-python
    sudo easy_install pip
    sudo pip install virtualenv

Create Database and User

    mysql -u root -p (login as MySQL admin)
    CREATE DATABASE lrs;
    CREATE USER 'lrs_user'@'localhost' identified by 'lrs_pass'
    GRANT ALL ON lrs.* TO 'lrs_user'@'localhost';
    exit (logout as MySQL admin)

Create Desired Directory for LRS

    mkdir lrs_folder
    cd lrs_folder

Clone UQ LRS Respository

    git clone https://github.com/CEIT-UQ/ADL_LRS.git
    cd ADL_LRS

Edit UQ LRS Settings

	vim adl_lrs/settings.py

Setup Virtual Environment

    fab setup_env
    source ../env/bin/activate 

Create UQ LRS Database

    fab setup_lrs (when prompted make lrs_admin a Django superuser)

Install NGINX

	yum install nginx
	
Setup NGINX

    vim /etc/nginx/conf.d/default.conf
    server {
        listen       80;
        server_name  lrs.ceit.uq.edu.au;
        access_log   /var/log/nginx/access_log;
        rewrite      ^ https://$server_name$request_uri? permanent;
    }

    vim /etc/nginx/conf.d/ssl.conf
    server {
	    listen       443;
	    server_name  lrs.ceit.uq.edu.au;
	    access_log /var/log/nginx/ssl_access_log;
	    ssl                  on;
	    ssl_certificate      /etc/pki/tls/certs/cert_file.crt;
	    ssl_certificate_key  /etc/pki/tls/private/cert.key;
	    ssl_session_timeout  5m;
	    ssl_protocols  SSLv2 SSLv3 TLSv1;
	    ssl_ciphers  HIGH:!aNULL:!MD5;
	    ssl_prefer_server_ciphers   on;
	    location / {
	        proxy_pass_header Server;
	        proxy_set_header Host $http_host;
	        proxy_redirect off;
	        proxy_set_header X-Forwarded-For $remote_addr;
	        proxy_set_header X-Scheme $scheme;
	        proxy_connect_timeout 10;
	        proxy_read_timeout 10;
	        proxy_pass http://127.0.0.1:8000/;
	    }
	}

## Start NGINX

    nginx
    
## Stop NGINX

    nginx -s stop

## Start LRS

While still in the ADL_LRS directory, run

    supervisord

To verify it's running

    supervisorctl

You should see a task named web running. This will host the application using gunicorn with 2 worker processes.

## Test LRS
    
    fab test_lrs

## Shutdown LRS

    supervisorctl (enter supervisor shell)
    stop all
    exit (leave supervisor shell)
    unlink /tmp/supervisor.sock

## 

## Helpful Information
    
* [Data Migrations with South](https://github.com/adlnet/ADL_LRS/wiki/DB-Migration-with-South)
* [Test Coverage](https://github.com/adlnet/ADL_LRS/wiki/Code-Coverage)
* [Code Profiling](https://github.com/adlnet/ADL_LRS/wiki/Code-Profiling-with-cProfile)
* [Sending Attachments](https://github.com/adlnet/ADL_LRS/wiki/Sending-Statements-with-Attachments)
* [Setting up Nginx](https://github.com/adlnet/ADL_LRS/wiki/Using-Nginx-for-Production)
* [OAuth Help](https://github.com/adlnet/ADL_LRS/wiki/Using-OAuth)

## License
   Copyright 2012 Advanced Distributed Learning

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.
