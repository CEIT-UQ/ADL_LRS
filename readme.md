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

## Start LRS
While still in the ADL_LRS directory, run

    supervisord

To verify it's running

    supervisorctl

You should see a task named web running. This will host the application using gunicorn with 2 worker processes.
If you open a browser and visit http://localhost:8000/xapi you will hit the LRS. Gunicorn does not serve static files
so no CSS will be present. This is fine if you're doing testing/development but if you want to host a production-ready
LRS, Nginx needs to be setup to work with Gunicorn to serve static files. Please read these instructions for including
Nginx. For a more detailed description of the tools being used in general, visit [here](https://github.com/adlnet/ADL_LRS/wiki/Putting-the-Pieces-Together).

## Test LRS
    
    fab test_lrs

## Shutdown LRS

    supervisorctl (note the process ID)
    kill process_id
    unlink /tmp/supervisor.sock

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
