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
$ GRANT ALL PREVILAGES ON DATABASE shop_db TO django;
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> If you are about to create more than one database just repeat only the first and last step to create database and grant permission to user on that database

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> To close the CLI  

```bash
$ \q
```
