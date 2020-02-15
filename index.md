# DEVOPS INTERVIEW TASK
*Epigraph: Сквозь пайплайн проплыл коммит "Simple fix" Кукуева. Весь продакшн поломала одна строчка хуева.*

## STEP 1. Building a microservice with the REST API
*Subtask*:
Build a basic microservice app with RESTful API using a language and some DB on your choice. App should perform CRUD operations in DB.

*Possible solutions*:
Python as a language, PostgreSQL as DB, Git as VCS, GitHub for the remote repository. Go is also an option but following examples and links will be for Python so you should do some more research work if you prefer to use Go. The same for the other languages.
Any IDE on your choice (PyCharm or Visual Studio Code). Personally I prefer VSCode with Python extension and Remote Development kit for using with server hosts.
Any Linux OS on the host recommended. It can be either a cloud VM (GCP for example) or a local machine. Ubuntu Server 18.04 running under VirtualBox with 2GB RAM allocated would be nice choice for Windows users.

*Python libraries*: Flask for building a RESTful API service, psycopg2 (recommended) or py-postgresql as Python DB adapter.

*Tips:*
Use Docker and Docker Compose to setup additional infrastructure such as DB or CI (and your app later).
Docker CE and Docker Compose must be installed on the host.
Don't use python2.7. Install more contemporary version such as 3.8 and always use venv.
Example for Ubuntu:
```
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt-get update
sudo apt-get install python3.8 python3.8-venv python3.8-dev
python3.8 -m pip install --upgrade pip setuptools wheel
```
Create venv in your project folder and add it to .gitignore file. Example:
```
mkdir -p restapp_project
cd restapp_project
git init
echo 'env/' > .gitignore
python3.8 -m venv env
source env/bin/activate
pip install --upgrade pip setuptools wheel
```
Then use pip to install the libraries.
You can use psql to query your DB:
`psql -h localhost <DB NAME> <USERNAME>`.
You can use either curl or Postman to test your app.
And don't forget to write README.md and some comments for your code.

*Links (Russian):*
* [Python basics for very newbies](https://stepik.org/course/67)
* [Git basics](https://git-scm.com/book/ru/v2/%D0%92%D0%B2%D0%B5%D0%B4%D0%B5%D0%BD%D0%B8%D0%B5-%D0%9E%D1%81%D0%BD%D0%BE%D0%B2%D1%8B-Git)
* [PostgreSQL](https://eax.me/postgresql-install/)
* [psycopg2](https://eax.me/python-psycopg2/) (install as psycopg2-binary)
* [py-postgresql](https://eax.me/python-postgresql/) (not working with latest PostgreSQL, use 9.6 in compose file)
* [Flask](https://eax.me/python-flask/)

*Links (English):*
* [Setting up PostgreSQL with Docker Compose](https://hub.docker.com/_/postgres)
* [Python + Flask + PostgreSQL on Medium](https://medium.com/@dushan14/create-a-web-application-with-python-flask-postgresql-and-deploy-on-heroku-243d548335cc)
* [Another Python + Flask + PostgreSQL article](https://www.codementor.io/@olawalealadeusi896/restful-api-with-python-flask-framework-and-postgres-db-part-1-kbrwbygx5)

*Definition of Done:*
Routes and CRUD operations for the app listed below. The API should:

### Return list with all users from the database for testing purposes
* **Request:** GET /users
* **Response code:** 200 OK
* **curl**: `curl -XGET 'localhost:5000/users'`

### Save the given user's name and date of birth in the database

* **Request:** POST /hello/\<username\> {“dateOfBirth": "YYYY-MM-DD" }
* **Response code:** 201 Created or 409 Conflict if user already exists.
* **Response message:** *New user '\<username\>' was added successfully.*
* **curl**: `curl -XPOST -H 'Content-Type: application/json' -d '{"dateOfBirth":"YYYY-MM-DD"}' 'localhost:5000/hello/<username>'`

### Save/update the given user's name and date of birth in the database

* **Request:** PUT /hello/\<username\> {“dateOfBirth": "YYYY-MM-DD" }
* **Response code:** 204 No Content or 201 Created if user doesn't exist.
* **curl**: `curl -XPUT -H 'Content-Type: application/json' -d '{"dateOfBirth":"YYYY-MM-DD"}' 'localhost:5000/hello/<username>'`

*Note:*
\<username\> must contains only letters.
YYYY-MM-DD must be a date before the today date.

### Return hello birthday message for the given user
* **Request:** GET /hello/\<username\>
* **Response code:** 200 OK or 404 Not Found if user doesn't exist.
* **Response messages:**
  * If username's birthday is in N days: *Hello, \<username\>! Your birthday is in N day(s)*
  * If username's birthday is today: *Hello, \<username\>! Happy birthday!*
* **curl**: `curl -XGET 'localhost:5000/hello/<username>'`

### Delete the given user from the database
* **Request:** DELETE /hello/\<username\>
* **Response code:** 200 OK or 404 Not Found if user doesn't exist.
* **Response messages:** *User \<username\> was deleted successfully.*
* **curl**: `curl -XDELETE 'localhost:5000/hello/<username>'`

## STEP 2. Containerizing application
*Subtask*:
Create a Dockerfile for your app and docker-compose.yml to deploy your app along with some DB.

*Solutions*: Docker and Docker Compose for basic setup of services.

*Tips:*
Docker Compose file could (and should) be already created to set up the DB. Now containerize your app with Dockerfile and add Flask app to docker-compose.yml
```
  web:
    build: .
    restart: always
    ports:
      - 5000:5000 # Default Flask app port
```

*Links (English):*
* [Dockerizing Flask with Postgres](https://testdriven.io/blog/dockerizing-flask-with-postgres-gunicorn-and-nginx/)
 
## STEP 3. Developing tests
*Subtask*:
Develop unit tests for your app with some testing framework on your choice.

*Possible solutions*:
*Python libraries*: UnitTest (Python standard library) or PyTest (advanced level)

*Tips:*
Recommended way to store test in a separate file (Python module such as tests.py) and use something like that (UnitTest example):
```
import unittest
import app
import xmlrunner
from json import dumps
from datetime import date

today = date.today()
tomorrow = date(today.year-1,today.month,today.day+1)
yesterday = date(today.year,today.month,today.day-1)

class TestHello(unittest.TestCase):

    def setUp(self):
        app.app.testing = True
        self.app = app.app.test_client()

    def test_root(self):
        rv = self.app.get('/')
        self.assertEqual(rv.status, '200 OK')
        self.assertEqual(rv.data, b'{"message": "<EXPECTED MESSAGE>"}\n')
```

*Links (Russian):*
* [Testing Flask app with UnitTest](https://flask-russian-docs.readthedocs.io/ru/latest/testing.html)
* [Article by OTUS](https://habr.com/ru/company/otus/blog/433572/) (see Flask section)
* [PyTest](https://habr.com/ru/post/269759/)

*Links (English):*
* [Jenkins CI Pipeline with Python (Flask + unittest)](https://medium.com/@Joachim8675309/jenkins-ci-pipeline-with-python-8bf1a0234ec3) (Just for now see only part 2)
* [Testing Flask app with PyTest](https://flask.palletsprojects.com/en/1.1.x/testing/)

## STEP 4. Building a pipeline
*Subtask*:
Create a Pipeline as a Code file in your repo for the CI tool that will run unit tests and build your app if they are passed.

*Possible solutions*:
Any CI tool on your choice. Jenkins would be a good option and the following examples are for Jenkins. Another options including Gitlab CI, Teamcity, etc.
*Docker image*: `jenkinsci/blueocean`

*Tips:*
Add Jenkins to your Docker Compose file. After basic set up you can choose two options: write your pipeline as shell script in Jenkins or write Pipeline as a Code with a separate Groovy script (advanced but recommended) and place it in your Git repo. Run your stages (`Build` and `Test`) in Docker containers. Your can also setup Postgres as sidecar container (advanced level).

*Note:*
If you will run your app on some cloud VPS it's better to deploy your app (the next step) before you starting to set up Jenkins pipeline in the WebUI because you can't push data from the Jenkins volume to the Git repo. Run Jenkins container locally with `docker-compose up` only for testing / research purposes. Otherwise if you will write Jenkinsfile you can easily repoduce this setup on VPS.

*Links (Russian):*
* [Something about basic Jenkins setup](https://smyt.ru/blog/razvorachivaem-jenkins-dlya-django-proekta-s-ispol/) (Don't use custom Dockerfile, use `jenkinsci/blueocean` image instead. See links in English.)

*Links (English):*
* [Jenkins Environment using Docker](https://medium.com/@Joachim8675309/jenkins-environment-using-docker-6a12603ebf9)
* [Jenkins CI Pipeline with Python (Flask + unittest)](https://medium.com/@Joachim8675309/jenkins-ci-pipeline-with-python-8bf1a0234ec3)

## STEP 5. Deploying microservices
*Subtask*:
Deploy your app along with DB and CI on some cloud VPS as available microservices.

*Possible solutions*:
GCP Free Tier is a good option.

*Tips:*
Set up some Ubuntu VPS on Google Cloud Platform. Copy your project to the server with `git clone`. Troubleshoot any issues with network accessibility for your services (your RESTful API and Jenkins) from the Internet. Expose necessary ports in other words.

*Links (Russian):*
* [Free VPS on Google Cloud](https://o7planning.org/ru/11853/create-free-vps-on-google-cloud)

*Links (English):*
* [Managing SSH keys in metadata on GCP](https://cloud.google.com/compute/docs/instances/adding-removing-ssh-keys)
* [Using firewall rules on GCP](https://cloud.google.com/vpc/docs/using-firewalls)

## STEP 6. Adding a trigger for job
*Subtask*:
Create a pipeline in your CI that will run build after each commit to the repo.

*Tips:*
Use GitHub webhook to complete the last step.

*Links (English):*
* [Integrate Jenkins with GitHub: build after each commit](https://www.youtube.com/watch?v=Z3S2gMBUkBo)

## DEFINITION OF DONE
* Provide link to the GitHub public repo with your project.
* Your code should contain at least app and test modules (*.py files), Dockerfile, docker-compose.yml, Jenkinsfile.
* Provide an IP address (or domain name) of your cloud VPS with the services setup, username and a public key to access VPS by SSH.
* There should be at least three running containers: PostgreSQL (localhost:5432), Jenkins (localhost:8080), Web (localhost:5000).
* Provide an absolute path to the directory with your project on VPS.
* Provide URLs for your API (`http://<YOUR VPS>:5000`), Jenkins WebUI (`http://<YOUR VPS>:8080`) and credentials (username:password) for Jenkins WebUI.
