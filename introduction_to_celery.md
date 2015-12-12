# Introduction to Celery

* What is a Task Queue?
* What do I need?
* Get Started
* Celery is…
* Features
* Framework Integration
* Quickjump
* Installation


## What is a Task Queue?


Task queues are used as a mechanism to distribute work across threads or machines.

A task queue’s input is a unit of work called a task. Dedicated worker processes constantly monitor task queues for new work to perform.

Celery communicates via messages, usually using a broker to mediate between clients and workers. To initiate a task, a client adds a message to the queue, which the broker then delivers to a worker.

A Celery system can consist of multiple workers and brokers, giving way to high availability and horizontal scaling.

Celery is written in Python, but the protocol can be implemented in any language. So far there’s RCelery for the Ruby programming language, node-celery for Node.js and a PHP client. Language interoperability can also be achieved by using webhooks.


## What do I need?


Version Requirements

Celery version 3.0 runs on

Python ❨2.5, 2.6, 2.7, 3.2, 3.3❩
PyPy ❨1.8, 1.9❩
Jython ❨2.5, 2.7❩.
This is the last version to support Python 2.5, and from the next version Python 2.6 or newer is required. The last version to support Python 2.4 was Celery series 2.2.

Celery requires a message transport to send and receive messages. The RabbitMQ and Redis broker transports are feature complete, but there’s also support for a myriad of other experimental solutions, including using SQLite for local development.

Celery can run on a single machine, on multiple machines, or even across data centers.


## Get Started


If this is the first time you’re trying to use Celery, or you are new to Celery 3.0 coming from previous versions then you should read our getting started tutorials:

First Steps with Celery
Next Steps

## Celery is…


Simple

Celery is easy to use and maintain, and it doesn’t need configuration files.

It has an active, friendly community you can talk to for support, including a mailing-list and an IRC channel.

Here’s one of the simplest applications you can make:

from celery import Celery

app = Celery('hello', broker='amqp://guest@localhost//')

@app.task
def hello():
    return 'hello world'
Highly Available

Workers and clients will automatically retry in the event of connection loss or failure, and some brokers support HA in way of Master/Master or Master/Slave replication.

Fast

A single Celery process can process millions of tasks a minute, with sub-millisecond round-trip latency (using RabbitMQ, py-librabbitmq, and optimized settings).

Flexible

Almost every part of Celery can be extended or used on its own, Custom pool implementations, serializers, compression schemes, logging, schedulers, consumers, producers, autoscalers, broker transports and much more.

It supports

Brokers

RabbitMQ, Redis,
MongoDB (exp), ZeroMQ (exp)
CouchDB (exp), SQLAlchemy (exp)
Django ORM (exp), Amazon SQS, (exp)
and more…
Concurrency

prefork (multiprocessing),
Eventlet, gevent
threads/single threaded
Result Stores

AMQP, Redis
memcached, MongoDB
SQLAlchemy, Django ORM
Apache Cassandra
Serialization

pickle, json, yaml, msgpack.
zlib, bzip2 compression.
Cryptographic message signing.
Features
Monitoring

A stream of monitoring events is emitted by workers and is used by built-in and external tools to tell you what your cluster is doing – in real-time.

Read more….

Workflows

Simple and complex workflows can be composed using a set of powerful primitives we call the “canvas”, including grouping, chaining, chunking and more.

Read more….

Time & Rate Limits

You can control how many tasks can be executed per second/minute/hour, or how long a task can be allowed to run, and this can be set as a default, for a specific worker or individually for each task type.

Read more….

Scheduling

You can specify the time to run a task in seconds or a datetime, or or you can use periodic tasks for recurring events based on a simple interval, or crontab expressions supporting minute, hour, day of week, day of month, and month of year.

Read more….

Autoreloading

In development workers can be configured to automatically reload source code as it changes, including inotify(7) support on Linux.

Read more….

Autoscaling

Dynamically resizing the worker pool depending on load, or custom metrics specified by the user, used to limit memory usage in shared hosting/cloud environments or to enforce a given quality of service.

Read more….

Resource Leak Protection

The --maxtasksperchild option is used for user tasks leaking resources, like memory or file descriptors, that are simply out of your control.

Read more….

User Components

Each worker component can be customized, and additional components can be defined by the user. The worker is built up using “bootsteps” — a dependency graph enabling fine grained control of the worker’s internals.

Framework Integration
Celery is easy to integrate with web frameworks, some of which even have integration packages:

Django	django-celery
Pyramid	pyramid_celery
Pylons	celery-pylons
Flask	not needed
web2py	web2py-celery
Tornado	tornado-celery
The integration packages are not strictly necessary, but they can make development easier, and sometimes they add important hooks like closing database connections at fork(2).

Quickjump
I want to ⟶

get the return value of a task
use logging from my task
learn about best practices
create a custom task base class
add a callback to a group of tasks
split a task into several chunks
optimize the worker
see a list of built-in task states
create custom task states
set a custom task name
track when a task starts
retry a task when it fails
get the id of the current task
know what queue a task was delivered to
see a list of running workers
purge all messages
inspect what the workers are doing
see what tasks a worker has registerd
migrate tasks to a new broker
see a list of event message types
contribute to Celery
learn about available configuration settings
receive email when a task fails
get a list of people and companies using Celery
write my own remote control command
change worker queues at runtime
Jump to ⟶

Brokers
Applications
Tasks
Calling
Workers
Daemonizing
Monitoring
Optimizing
Security
Routing
Configuration
Django
Contributing
Signals
FAQ
API Reference
Installation
You can install Celery either via the Python Package Index (PyPI) or from source.

To install using pip,:

$ pip install -U Celery
To install using easy_install,:

$ easy_install -U Celery
Bundles
Celery also defines a group of bundles that can be used to install Celery and the dependencies for a given feature.

You can specify these in your requirements or on the pip comand-line by using brackets. Multiple bundles can be specified by separating them by commas.

$ pip install "celery[librabbitmq]"

$ pip install "celery[librabbitmq,redis,auth,msgpack]"
The following bundles are available:

Serializers
celery[auth]:	for using the auth serializer.
celery[msgpack]:
 	for using the msgpack serializer.
celery[yaml]:	for using the yaml serializer.
Concurrency
celery[eventlet]:
 	for using the eventlet pool.
celery[gevent]:	for using the gevent pool.
celery[threads]:
 	for using the thread pool.
Transports and Backends
celery[librabbitmq]:
 	for using the librabbitmq C library.
celery[redis]:	for using Redis as a message transport or as a result backend.
celery[mongodb]:
 	for using MongoDB as a message transport (experimental), or as a result backend (supported).
celery[sqs]:	for using Amazon SQS as a message transport (experimental).
celery[memcache]:
 	for using memcached as a result backend.
celery[cassandra]:
 	for using Apache Cassandra as a result backend.
celery[couchdb]:
 	for using CouchDB as a message transport (experimental).
celery[couchbase]:
 	for using CouchBase as a result backend.
celery[beanstalk]:
 	for using Beanstalk as a message transport (experimental).
celery[zookeeper]:
 	for using Zookeeper as a message transport.
celery[zeromq]:	for using ZeroMQ as a message transport (experimental).
celery[sqlalchemy]:
 	for using SQLAlchemy as a message transport (experimental), or as a result backend (supported).
celery[pyro]:	for using the Pyro4 message transport (experimental).
celery[slmq]:	for using the SoftLayer Message Queue transport (experimental).
Downloading and installing from source
Download the latest version of Celery from http://pypi.python.org/pypi/celery/

You can install it by doing the following,:

$ tar xvfz celery-0.0.0.tar.gz
$ cd celery-0.0.0
$ python setup.py build
# python setup.py install
The last command must be executed as a privileged user if you are not currently using a virtualenv.

Using the development version
With pip
The Celery development version also requires the development versions of kombu, amqp and billiard.

You can install the latest snapshot of these using the following pip commands:

$ pip install https://github.com/celery/celery/zipball/master#egg=celery
$ pip install https://github.com/celery/billiard/zipball/master#egg=billiard
$ pip install https://github.com/celery/py-amqp/zipball/master#egg=amqp
$ pip install https://github.com/celery/kombu/zipball/master#egg=kombu
With git
Please the Contributing section.