# Thread pools

There are multiple thread pools that are configurable in UniConfig:

* Jetty server,
* Task executor,
* Notifications,
* SSH Client,
* NetConf topology,
* CLI topology.


## Jetty server

Jetty server is used to aggregate connectors (HTTP request receivers) and request handlers.
Connectors use the thread pool methods to run jobs that will eventually call the handle method.

Available parameters to configure:
* **jetty.max-threads=200**
  * The maximum threads available in the jetty server. The default value is 200.
* **jetty.min-threads=8** 
  * The minimum threads available in the jetty server. The default value is 80.
* **jetty.idle-timeout=60**
  * Threads that are idle for longer than this period may be stopped (in seconds).
    The default value is 60.

If any of these parameters are left empty (e.g. **jetty.max-threads=**),
the default value will be set.


## Task Executor

Task executor is used to execute operations (internal operations or RPCs) either synchronously, or
asynchronously on given nodes / devices.

* **task-executor.max-queue-capacity=10000** 
  * The maximum queue capacity for postponed tasks. The default value is 10000.
* **task-executor.max-cpu-load=0.9** 
  * The maximum cpu load for executing tasks. Cpu load is set in double percentage where 1.0 -> 100% cpu load, 0.9 -> 90%.
    The default value is 0.9.
* **task-executor.default-thread-count=** 
  * The efault thread count used for executing tasks. The default value is the amount of available processors * 2.
* **task-executor.max-thread-count=** 
  * The maximum thread count used for executing tasks. The default value is **default-thread-count** * 20.
* **task-executor.keepalive-time=60** 
  * The time in seconds before the execution of a specified task will time out. The default value is 60.

If any of these parameters are left empty (e.g. **task-executor.default-thread-count=**), 
the default value will be set. 


## Notifications

A NetConf related thread pool that handles notification subscriptions (acquiring of subscriptions, 
release of subscriptions etc.).

* **notifications.thread-parameters.monitoring-executor-initial-pool-size=** 
  * The initial thread count used by the monitoring executor. The default value is the amount of available processors.)
* **notifications.thread-parameters.monitoring-executor-maximum-pool-size=** 
  * The maximum thread count used by the monitoring executor. The default value is **initial-pool-size** * 4.
* **notifications.thread-parameters.monitoring-executor-keepalive-time=60** 
  * The time in seconds before the execution of a specified task will time out in the
    monitoring executor. The default value is 60.

If any of these parameters are left empty (e.g. **notifications.thread-parameters.monitoring-executor-initial-pool-size=**), 
the default value will be set.


## SSH Client

SSH Client uses a thread pool that handles communication to devices. This thread pool is shared between NetConf and CLI topologies.

* **ssh-client.default-timeout=-1**
  * Timeout for ssh connections in seconds. If set to a negative value, timeouts will be disabled. The default value is -1.
* **ssh-client.heartbeat-interval=30**
  * The interval in which the client pings the server to see if the connection is still alive. The default is 30 seconds.
* **ssh-client.heartbeat-reply-wait=60**
  * Key used to indicate that the heartbeat request is also expecting a reply - time in seconds to wait for the reply. 
  If non-positive then no reply is expected. The default value is 60 seconds.
* **ssh-client.heartbeat-request=keepalive@sshd.apache.org**
  * Key used to set the heartbeat request that should be sent to the server. The default value is ***keepalive@sshd.apache.org***.
* **ssh-client.ssh-default-nio-workers=8**
  * The amount of non-blocking workers that will handle communication messages. The default value is 8.

If any of these parameters are left empty (e.g. **ssh-client.ssh-default-nio-workers=**),
the default value will be set.

## NetConf Topology

NetConf topology thread pools are used to connect to and keep the connection alive to NetConf devices.

* **netconf-topology-parameters.fixed-thread-pool-thread-count=2**
  * The fixed thread pool thread count in NetConf topology. This thread pool is used for reading device capabilities 
  and schema set up. The default value is 2.
* **netconf-topology-parameters.scheduled-thread-pool-thread-count=2** 
  * The scheduled thread pool thread count in NetConf topology. his thread pool is used for scheduling of keepalive 
  messages. The default value is 2.

If any of these parameters are left empty (e.g. **netconf-topology-parameters.fixed-thread-pool-thread-count=**),
the default value will be set.


## CLI Topology

CLI topology thread pools are used to connect to and keep the connection alive to CLI devices.

* **cli-topology-parameters.keepalive-thread-count=** 
  * The thread pool count dedicated ONLY for keepalive and reconnect scheduling.
  The default will be either 2 or the amount of available processors, whichever is higher.
* **cli-topology-parameters.init-executor-thread-timeout=120** - (If any of the threads is unused for X seconds, 
it will be stopped and recreated in the future if necessary.)
* **cli-topology-parameters.init-executor-thread-count=** 
  * The max number of threads for the flexible thread pool executor. This thread pool is used to process events and asynchronous locking of the CLI layer.
    The default will be available processors * 8.

If any of these parameters are left empty (e.g. **cli-topology-parameters.keepalive-thread-count=**),
the default value will be set.
