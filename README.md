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
