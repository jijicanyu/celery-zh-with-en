# Using Redis


## Installation

For the Redis support you have to install additional dependencies. You can install both Celery and these dependencies in one go using the celery[redis] [bundle](http://docs.celeryproject.org/en/latest/getting-started/introduction.html#bundles):

```python
$ pip install -U celery[redis]
```

## Configuration

Configuration is easy, just configure the location of your Redis database:

```
BROKER_URL = 'redis://localhost:6379/0'
```
Where the URL is in the format of:

```
redis://:password@hostname:port/db_number
```
all fields after the scheme are optional, and will default to localhost on port 6379, using database 0.

If a unix socket connection should be used, the URL needs to be in the format:

```
redis+socket:///path/to/redis.sock
```

### Visibility Timeout

The visibility timeout defines the number of seconds to wait for the worker to acknowledge the task before the message is redelivered to another worker. Be sure to see [Caveats](http://docs.celeryproject.org/en/latest/getting-started/brokers/redis.html#redis-caveats) below.

This option is set via the [BROKER_TRANSPORT_OPTIONS](http://docs.celeryproject.org/en/latest/configuration.html#std:setting-BROKER_TRANSPORT_OPTIONS) setting:

BROKER_TRANSPORT_OPTIONS = {'visibility_timeout': 3600}  # 1 hour.
The default visibility timeout for Redis is 1 hour.


### Results

If you also want to store the state and return values of tasks in Redis, you should configure these settings:

```
CELERY_RESULT_BACKEND = 'redis://localhost:6379/0'
```
For a complete list of options supported by the Redis result backend, see [Redis backend settings](http://docs.celeryproject.org/en/latest/configuration.html#conf-redis-result-backend)


## Caveats

* Broadcast messages will be seen by all virtual hosts by default.

 You have to set a transport option to prefix the messages so that they will only be received by the active virtual host:

 ```
 BROKER_TRANSPORT_OPTIONS = {'fanout_prefix': True}
 ```  
 Note that you will not be able to communicate with workers running older versions or workers that does not have this setting enabled.

 This setting will be the default in the future, so better to migrate sooner rather than later.

* Workers will receive all task related events by default.

 To avoid this you must set the fanout_patterns fanout option so that the workers may only subscribe to worker related events:

 ```
 BROKER_TRANSPORT_OPTIONS = {'fanout_patterns': True}
 ```
 Note that this change is backward incompatible so all workers in the cluster must have this option enabled, or else they will not be able to communicate.

 This option will be enabled by default in the future.

* If a task is not acknowledged within the [Visibility Timeout](http://docs.celeryproject.org/en/latest/getting-started/brokers/redis.html#redis-visibility-timeout) the task will be redelivered to another worker and executed.

 This causes problems with ETA/countdown/retry tasks where the time to execute exceeds the visibility timeout; in fact if that happens it will be executed again, and again in a loop.

 So you have to increase the visibility timeout to match the time of the longest ETA you are planning to use.

 Note that Celery will redeliver messages at worker shutdown, so having a long visibility timeout will only delay the redelivery of ‘lost’ tasks in the event of a power failure or forcefully terminated workers.

 Periodic tasks will not be affected by the visibility timeout, as this is a concept separate from ETA/countdown.

 You can increase this timeout by configuring a transport option with the same name:

 `BROKER_TRANSPORT_OPTIONS = {'visibility_timeout': 43200}`  
 The value must be an int describing the number of seconds.

* Monitoring events (as used by flower and other tools) are global and is not affected by the virtual host setting.

  This is caused by a limitation in Redis. The Redis PUB/SUB channels are global and not affected by the database number.

* Redis may evict keys from the database in some situations

 If you experience an error like:

 ```
 InconsistencyError, Probably the key ('_kombu.binding.celery') has been
 removed from the Redis database.
 ```
 you may want to configure the redis-server to not evict keys by setting the *timeout* parameter to 0.