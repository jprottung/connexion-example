========================
Udacity Capstone Project
========================

This is the solution from Janik Prottung to the Udacity Cloud DevOps Nanodegree Capstone project.
As a base I used the `hjacobs/connexion-example <https://github.com/hjacobs/connexion-example>`_.
The "old" readme can be found below the next Title.

What I did to the old repo
--------------------------

I changed the app.py to contain one pet from the start and updated the Dockerfile to meet the linting
requirements.

How you can bring up the Cluster
--------------------------------

1. Update parameters in the `cloud_formation` sub-folder.
    * Name the `Environment` in both json-files
    * Update the EKS-AMI to the one from your zone
2. Create the cloudformation stacks
    a) network: `cloud_formation/create.bat <stack-name> network.yml network-parameters.json`
    b) cluster (after network completed): `cloud_formation/create.bat <stack-name> cluster.yml cluster-parameters.json`
3. Set up kubectl to talk to your cluster
    * Tips on `Create Kubeconfig <https://docs.aws.amazon.com/de_de/eks/latest/userguide/create-kubeconfig.html>`_
    * Command: `aws eks --region <region-code> update-kubeconfig --name <cluster_name>`
4. Apply the deployments and services to your cluster
    a) Replace `<<ReplaceWith-NodeInstanceRole-Arn>>` in `kubernetes/aws-auth-cm.yaml` with cloudformation export
    b) Replace `<<ReplaceWith-NodeGroupAZ1Name>>` and `<<ReplaceWith-NodeGroupAZ2Name>>` in `kubernetes/cluster-autoscaler` with cloudformation export
    c) Apply config `kubectl -f kubernetes/aws-auth-cm.yml`
    d) Apply config `kubectl -f kubernetes/cluster-autoscaler.yml`
    e) Apply config `kubectl -f kubernetes/connexion-loadbalancer.yml`
    f) Apply config `kubectl -f kubernetes/connexion-deployment.yml`
5. Check Everything works
    * Get Public DNS from `kubectl get svc` and visit in your browser with the path `/pets`
    * You should see Paula now

How to deploy using Jenkins
---------------------------

1. Set Up Jenkins
    1. Install Jenkins
    2. Install docker, aws-cli, kubectl
    3. Set docker, aws and kubectl permissions for user jenkins
    4. Add Docker-Hub credentials with Id `dockerhub`
    5. Run `aws configure` and enter credentials (same user) and region
    6. Run `aws eks --region <region-code> update-kubeconfig --name <cluster_name>`
    7. Copy `.kube` and `.aws` folders to jenkins home directory and set permissions for jenkins user
2. Create Pipeline using Git-repository
3. Run Pipeline

==============================
Connexion Example REST Service
==============================

This example application implements a very basic "pet shop" REST service using the `Connexion`_ Python library.

Connexion is a framework on top of Flask_ to automagically handle your REST API requests
based on `Swagger 2.0 Specification`_ files in YAML.

👉 Please also have a look at the `Connexion Example with Redis database and Kubernetes deployment manifests`_.


Features
--------

This example application shows various features supported by the Connexion library:

* mapping of REST operations to Python functions (using the ``operationId`` in ``swagger.yaml``)

  * maps path, query and body parameters to keyword arguments

* bundled Swagger UI (served on `/ui/`_ path)
* automatic JSON serialization for ``application/json`` content type
* schema validation for the HTTP request body and query parameters:

  * required object properties
  * primitive JSON types (string, integers, etc)
  * date/time values
  * string lengths
  * minimum/maximum values
  * regular expression patterns

* gevent WSGI server
* OAuth2 protection


Files
-----

The example application only needs very few files:

* ``swagger.yaml``: the pet shop REST API Swagger definition
* ``app.py``: implementation of the pet shop operations with in-memory storage
* ``Pipfile``: list of required Python libraries (used by Pipenv)
* ``Dockerfile``: to build the example as a runnable Docker image
* ``test.sh``: shell script to execute example HTTP requests against the pet shop API


Running Locally
---------------

You can run the Python application directly on your local operating system (this requires Python 3 and `Pipenv <https://docs.pipenv.org/>`_):

.. code-block:: bash

    $ pipenv install --dev && pipenv shell
    $ ./app.py # start the HTTP server
    $ xdg-open http://localhost:8080/ui/
    $ ./test.sh # do some test HTTP requests


Running with Docker
-------------------

You can build the example application as a Docker image and run it:

.. code-block:: bash

    $ docker build -t connexion-example .
    $ docker run -d -p 8080:8080 connexion-example
    $ ./test.sh # do some test HTTP requests


Using OAuth2 Security
---------------------

To enable OAuth2 security (token verification), you need to pass the URL to the "tokeninfo" endpoint:

.. code-block:: bash

    $ docker run -d -p 8080:8080 -e HTTP_TOKENINFO_URL=https://auth.example.org/tokeninfo connexion-example

Using Connexion with a WSGI container
-------------------------------------

You can use the Flask WSGI app with any WSGI container, e.g. `using Flask with uWSGI`_:

.. code-block:: bash

    $ sudo pip3 install uwsgi
    $ uwsgi --http :8080 -w app

You can run uwsgi with a large number of worker processes to get high concurrency.
This obviously makes no sense for the in-memory pet store example (every worker would have its own pet store dictionary):

.. code-block:: bash

    $ uwsgi --http :8080 -w app -p 16  # use 16 worker processes

See the `uWSGI documentation`_ for more information.

.. _Connexion: https://pypi.python.org/pypi/connexion
.. _Flask: http://flask.pocoo.org/
.. _Swagger 2.0 Specification: https://github.com/swagger-api/swagger-spec/blob/master/versions/2.0.md
.. _/ui/: http://localhost:8080/ui/
.. _using Flask with uWSGI: http://flask.pocoo.org/docs/latest/deploying/uwsgi/
.. _uWSGI documentation: https://uwsgi-docs.readthedocs.org/
.. _Connexion Example with Redis database and Kubernetes deployment manifests: https://github.com/hjacobs/connexion-example-redis-kubernetes
