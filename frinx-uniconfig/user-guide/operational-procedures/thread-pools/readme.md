# Thread pools

There are several thread pools that can be configured in UniConfig:

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
  * The maximum number of threads available in the jetty server. The default value is 200.
* **jetty.min-threads=8** 
  * The minimum number of threads available in the jetty server. The default value is 80.
* **jetty.idle-timeout=60**
  * Threads that are idle for longer than this period (in seconds) can be stopped.
    The default value is 60.

If any of these parameters are left empty (e.g. **jetty.max-threads=**),
the default value is used.


## Task Executor

The task executor is used to execute operations (internal operations or RPCs), either synchronously or
asynchronously, on given nodes or devices.

* **task-executor.max-queue-capacity=10000** 
  * The maximum queue capacity for postponed tasks. The default value is 10000.
* **task-executor.max-cpu-load=0.9** 
  * The maximum CPU load for executing tasks. Load is expressed as a ratio so that 1.0 corresponds to 100% load, 0.9 to 90%, etc. The default value is 0.9.
* **task-executor.default-thread-count=** 
  * The efault thread count used for executing tasks. The default value is the number of available processors * 2.
* **task-executor.max-thread-count=** 
  * The maximum thread count used for executing tasks. The default value is **default-thread-count** * 20.
* **task-executor.keepalive-time=60** 
  * The time in seconds before the execution of a specified task is timed out. The default value is 60.

If any of these parameters are left empty (e.g. **task-executor.default-thread-count=**), 
the default value is used. 


## Notifications

A NetConf related thread pool that handles notification subscriptions (acquiring of subscriptions, 
release of subscriptions, etc.).

* **notifications.thread-parameters.monitoring-executor-initial-pool-size=** 
  * The initial thread count used by the monitoring executor. The default value is the number of available processors.
* **notifications.thread-parameters.monitoring-executor-maximum-pool-size=** 
  * The maximum thread count used by the monitoring executor. The default value is **initial-pool-size** * 4.
* **notifications.thread-parameters.monitoring-executor-keepalive-time=60** 
  * The time in seconds before the execution of a specified task is timed out in the
    monitoring executor. The default value is 60.

If any of these parameters are left empty (e.g. **notifications.thread-parameters.monitoring-executor-initial-pool-size=**), 
the default value is used.


## SSH Client

SSH Client uses a thread pool that handles communication with devices. This thread pool is shared between NetConf and CLI topologies.

* **ssh-client.default-timeout=-1**
  * Timeout for SSH connections (in seconds). If set to a negative value, timeouts are disabled. The default value is -1.
* **ssh-client.heartbeat-interval=30**
  * The interval (in seconds) at which the client pings the server to check if the connection is still alive. The default value is 30.
* **ssh-client.heartbeat-reply-wait=60**
  * Indicates if the heartbeat request expects a reply. Time (in seconds) to wait for a reply, a non-positive value means that no reply is expected. The default value is 60.
* **ssh-client.heartbeat-request=keepalive@sshd.apache.org**
  * The heartbeat request that is sent to the server. The default value is ***keepalive@sshd.apache.org***.
* **ssh-client.ssh-default-nio-workers=8**
  * The amount of non-blocking workers that handle communication messages. The default value is 8.

If any of these parameters are left empty (e.g. **ssh-client.ssh-default-nio-workers=**),
the default value is used.

## NetConf Topology

NetConf topology thread pools are used to connect to NetConf devices and keep the connection alive.

* **netconf-topology-parameters.fixed-thread-pool-thread-count=2**
  * The fixed thread pool thread count in the NetConf topology. Used to read device capabilities 
  and schema set up. The default value is 2.
* **netconf-topology-parameters.scheduled-thread-pool-thread-count=2** 
  * The scheduled thread pool thread count in the NetConf topology. Used to schedule keepalive 
  messages. The default value is 2.

If any of these parameters are left empty (e.g. **netconf-topology-parameters.fixed-thread-pool-thread-count=**),
the default value is used.


## CLI Topology

CLI topology thread pools are used to connect to CLI devices and keep the connection alive.

* **cli-topology-parameters.keepalive-thread-count=** 
  * The thread pool count dedicated ONLY to keepalive and reconnect scheduling.
  The default is either 2 or the number of available processors, whichever is higher.
* **cli-topology-parameters.init-executor-thread-timeout=120**
  * If any thread is unused for this period (in seconds), it is stopped and recreated in the future if necessary.
* **cli-topology-parameters.init-executor-thread-count=** 
  * The maximum, number of threads for the flexible thread pool executor. This thread pool is used to process events and asynchronous locking of the CLI layer. The default is the number of available processors * 8.

If any of these parameters are left empty (e.g. **cli-topology-parameters.keepalive-thread-count=**),
the default value is used.
