# Django-Aws-Deployment-Documentation

## _1. Deployment-Ready project_
&emsp;&emsp; First and foremost thing to do is to make our application production ready. Before
Deploying make sure you have done the following things.

1. Remove all print statements
2. In the Django app go to the project root directory and open settings.py file and change ``` DEBUG = False ```.
3. Add the credentials like application's secret key and database passwords to environment variables. Since we have multiple server instances and multiple databases in each instance by using environment variables all credentials can be handled in one env file and that can be used all over the project.
    #### To create Environment variables:
   <img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;">Various packages are available for creating environment variables in python. We use **python-dotenv**. Install the below pip package via terminal.

   ```bash 
      $ pip install python-dotenv
      ```
   <img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;">create a .env file in the same folder which contains the file where we're going to use environment variables. and add the key and value as shown below.

   > NOTE: value of environment key should be always a string

   ```bash
    SECRET_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
   ```

   **Usage:**

      ```python 
      from dotenv import load_dotenv
      
      # read the env file ()
      load_dotenv()
      
      # accessing the env variable
      SECRET_KEY = os.environ['SECRET_KEY']
      ```
5. Create a ```requirements.txt``` file and remove twisted.io from the list of dependencies because it is for Windows os we don't need it ubuntu.
    ```bash
    pip freeze > requirements.txt
    ```

## _2. Cloud Server Instance_
&emsp;&emsp; Many Cloud hosting servers like GCP, Azure and AWS are available. And we are going to deploy our project in AWS EC2 instance.

&emsp;&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Create a micro or small EC2 instance based on the server requirements. And we are going to use ubuntu virtual OS.

&emsp;&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> After selecting the apt server requirements. Launch the instance. 

&emsp;&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> The next step would be Generating a key-value pair (.ppk) file for instance or use the existing key-value pair. In both case download and secure the key-value pair for future use.

&emsp;&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Once the Instance gets created. It would be listed in the available instances. Once the Instance starts running we can continue the further installations.

## _3. Send Files to Server Using Filezilla_

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> I'm using Filezilla Software for file transfering to server.

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Open Filezilla click file select Site Manager create new site  select SSH File Transfer Protocol, Host add your instance Public IPv4 address, Username ubuntu(whatever), Logon Type select keyfile and connect the server.

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> This will open two file locations Local Site and Remote Site.

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Local Site is our project Localy running destination so we have to upload whole files in to ``` /home/ubuntu/ ``` path in Remote Site.

## _4. Server Dependencies installation_

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Open Putty Configuration and select SSH, Auth Select the .ppk file, then select Session add  Hostname ``` ubuntu@PublcDNS ```  , Create new session save the session and open.

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Make sure  ``` ls ``` to list the files directory.

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Get the ubuntu updates

```bash
$ sudo apt-get update
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Install the updates

```bash
$ sudo apt-get upgrade -y
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Install python virtual environment

```bash
$ sudo apt-get install python3-venv
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Create New Virtual Environment ```env```

```bash
$ python3 -m venv env
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Activate the virtual environment

```bash
$ source env/bin/activate
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Install python and postgres requirements

```bash
$ sudo apt install python3-venv python3-dev libpq-dev postgresql postgresql-contrib nginx curl
```
## _5. Postgres Installation and Configurations_
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> We have already installed postgresql and its dependencies. Lets start the configurations. open the postgres CLI

```bash
$ sudo -u postgres psql
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Create database ```shop_db```

```bash
$ CREATE DATABASE shop_db;
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Create postgres user with username as ```django``` and password as ```password```

```bash
$ CREATE USER django WITH PASSWORD 'password';
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Set database encoding 

```bash
$ ALTER ROLE django SET client_encoding TO 'utf8';
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Set role 

```bash
$ ALTER ROLE django SET default_transaction_isolation TO 'read committed';
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Set Timezone 

```bash
$ ALTER ROLE django SET timezone TO 'UTC';
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Grant permissions for user ```django``` on the database ```shop_db```. 

```bash
$ GRANT ALL PRIVILEGES ON DATABASE shop_db TO django;
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> If you are about to create more than one database just repeat only the first and last step to create database and grant permission to user on that database

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> To close the CLI  

```bash
$ \q
```

## _6. Django application Installation_

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Install django

```bash
$ pip3 install django
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Install postgres requirement for django

```bash
$ pip3 install psycopg2-binary
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Install wheel for installing pip packages

```bash
$ pip3 install wheel
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;">Install the dependencies for our project. Make sure while installing pip packages environment should be active.

```bash
$ pip3 install -r requirements.txt
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;">Now, you can migrate the initial database schema to our PostgreSQL database using the management script.

```bash
$ python manage.py makemigrations
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;">Now, you can migrate the changes to our database.

```bash
$ python manage.py migrate
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;">Create an administrative user for the project.

```bash
$ python manage.py createsuperuser
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;">You can collect all of the static content into the directory location that you configured.

```bash
$ python manage.py collectstatic
```

## _7. Webserver Configuration_

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Install nginx

```bash
$ sudo apt-get install -y nginx
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Start nginx

```bash
$ sudo nginx
```
>NOTE: If it says "Already in use". That means nginx installation is successful.

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Install gunicorn

```bash
$ pip3 install gunicorn
```
>NOTE: gunicorn should be installed from pypi not from ubuntu's repository.

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> To check the working of gunicorn. run it port 8000 and visit the server IP in browser adding port 8000 at the end. you should see our django app running. If not something has gone wrong so check all the above said process are done correctly.

```bash
$ gunicorn --bind 0.0.0.0:8000 <projec name>.wsgi:application

# If you get connection error. Then it means some gunicorn worker is already running in the same port. use the below command to stop all gunicorn workers.
$ sudo killall gunicorn
```
> NOTE: sometimes error may come from our django app and that will get printed. Rectify that error and use the above command and run again.

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> To check gunicorn status

```bash
$ sudo service gunicorn status
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Once the gunicorn and our django application runs fine. The next step would be installing and configuring ```supervisor```. We need supervisor to run the gunicorn continuously even after the server restarts. So the gunicorn will server our django application without any block.

```bash
$ sudo apt-get install -y supervisor
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Go to the supervisor root directory to add new configuration for running gunicorn.

```bash
$ cd /etc/supervisor/conf.d
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Create and edit file ```gunicorn.conf```.
```bash
$ sudo nano gunicorn.conf
```
```bash
# Add the following lines.
[program:gunicorn]
directory=/home/ubuntu # root directory
 # first one is the location of gunicorn and second location should be root location of project to create the sock file
command=/home/ubuntu/env/bin/gunicorn --workers 3 --bind unix:/home/ubuntu/app.sock <project-name>.wsgi:application
autostart=true
autorestart=true
stderr_logfile=/var/log/gunicorn/gunicorn.err.log
stdout_logfile=/var/log/gunicorn/gunicorn.out.log

[group:guni]
programs:gunicorn
```
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Create gunicorn log directory.
```bash
$ sudo mkdir /var/log/gunicorn
```
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Tell supervisor to read the newly added configuration file.
```bash
$ sudo supervisorctl reread
```
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Make supervisor to update and run all process in background.
```bash
$ sudo supervisorctl update
```
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> To check supervisor status.
```bash
$ sudo supervisorctl status
```
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> some other supervisor commands.
```bash
# to start all supervisor files
$ sudo supervisorctl start all

# to stop all supervisor files
$ sudo supervisorctl stop all

# to restart all supervisor files
$ sudo supervisorctl restart all
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Lets configure nginx to read the gunicorn sock file. Go to nginx root directory
```bash
$ cd /etc/nginx/sites-avilable/
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Create and edit the file ```django.conf```.
```bash
$ sudo nano django.conf
```
```bash
# Add the following lines

server_names_hash_bucket_size 128;

server {
    listen 80;
        server_name <IP>;
        # gunicorn sock file location
        location / {
                     include proxy_params;
                     proxy_pass http://unix:/home/ubuntu/app.sock;
        }
        # static files location
        location /static {
                      autoindex on;
                      alias /home/ubuntu/static;
        }
        # static cdn files location
        location /static_cdn {
                      autoindex on;
                      alias /home/ubuntu/static_cdn;
        }
        # media cdn files location
        location /media_cdn {
                      autoindex on;
                      alias /home/ubuntu/media_cdn;
        }

}
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> sym link site-available with sites-enabled.
```bash
$ sudo ln django.conf /etc/nginx/sites-enabled/
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Test nginx.
```bash
$ sudo nginx -t
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Restart nginx.
```bash
$ sudo service nginx  restart
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> To check nginx status.
```bash
$ sudo service nginx  status
```
