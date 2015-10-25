# JRuyi

A Java framework for easily developing network applications as you wish.

## Introduction

JRuyi is a Java framework for easily developing **efficient**, **scalable** and **flexible** network applications.  It hides the Java socket API by providing an event-driven asynchronous API over various transports, such as TCP and UDP, via [Java NIO](http://en.wikipedia.org/wiki/New_I/O).

!!! note "What is Ruyi?"
    Ruyi (Chinese: 如意; pinyin: rúyì; Wade–Giles: ju-i; literally "as [one] wishes; as [you] wish") is a curved
    decorative object that is a ceremonial sceptre in Chinese Buddhism or a talisman symbolizing power and good fortune
    in Chinese folklore.<br><br>
    Read [more ...](http://en.wikipedia.org/wiki/Ruyi_\(scepter\))

The key features of JRuyi are listed as follows.

#### Modularity
JRuyi is built on OSGi framework which is a dynamic module system and service platform.

#### Service Oriented
JRuyi is an OSGi based framework; its functionality is mainly provided through services.

#### Asynchronism
JRuyi provides an event-driven asynchronous IO framework.

#### Performance
High throughput, low latency; provides a thread-local cache mechanism to avoid frequent creation of large objects such as buffers; provides chained buffers to minimize unnecessary memory copy.

#### TCP Connection Pooling and Multiplexing
Provides an efficient IO service supporting TCP connection pooling and multiplexing.

#### Extensible Command Line
A command-line/shell backed by Apache Felix Gogo Runtime. New commands can be easily added via OSGi services.

#### Dynamic Configuration
Configurations can be created and updated dynamically through ruyi-cli.

#### Hot Deployment
Bundles can be installed, updated, started, stopped and uninstalled on the fly through ruyi-cli.

## Download

JRuyi is available in two distributions: `jruyi` and `jruyi-core`.

* `jruyi` - An OSGi based runtime which provides a lightweight container.
* `jruyi-core` - A single jar library(OSGi bundle) for being easily embedded/integrated into any runtime.

### Get jruyi

* [jruyi-2.4.1.zip](https://github.com/jruyi/jruyi/releases/download/v2.4.1/jruyi-2.4.1.zip) [[MD5](https://github.com/jruyi/jruyi/releases/download/v2.4.1/jruyi-2.4.1.zip.md5)] [[SHA1](https://github.com/jruyi/jruyi/releases/download/v2.4.1/jruyi-2.4.1.zip.sha1)]
* [jruyi-2.4.1.tar.gz](https://github.com/jruyi/jruyi/releases/download/v2.4.1/jruyi-2.4.1.tar.gz) [[MD5](https://github.com/jruyi/jruyi/releases/download/v2.4.1/jruyi-2.4.1.tar.gz.md5)] [[SHA1](https://github.com/jruyi/jruyi/releases/download/v2.4.1/jruyi-2.4.1.tar.gz.sha1)]

All previous releases of JRuyi can be found [here](https://github.com/jruyi/jruyi/releases).

### Get jruyi-core

The jar is available from JCenter: [jruyi-core-2.4.1.jar](https://jcenter.bintray.com/org/jruyi/jruyi-core/2.4.1/jruyi-core-2.4.1.jar).

To get jruyi-core through Maven, please add the following to your POM.

```pom
<repositories>
    <repository>
        <id>jcenter</id>
        <url>https://jcenter.bintray.com/</url>
    </repository>
</repositories>

<dependencies>
    <dependency>
        <groupId>org.jruyi</groupId>
        <artifactId>jruyi-core</artifactId>
        <version>2.4.1</version>
    </dependency>
</dependencies>
```

To get jruyi-core through Gradle, please add the following to your build script.

```gradle
repositories {
    jcenter()
}

dependencies {
    compile "org.jruyi:jruyi-core:2.4.1"
}
```

## Software Requirements

Oracle/OpenJDK JDK 7+ is required to run JRuyi.

## Getting a Feel for JRuyi

Please go to [Download](#download) to get the latest release of JRuyi. Unpack the archive that you downloaded, and then
you will get the directory `jruyi-2.4.1` which will be referred as $JRUYI_HOME.

!!! note "Note:"
    All the instructions in this section are given under a UNIX-like environment. It should be very straightforward for
    you to make them work on Windows. Just use the counterpart BAT command for a shell script mentioned later in this
    section. For example, use ruyi.bat for ruyi and ruyi-cli.bat for ruyi-cli.

Please open a console, go to $JRUYI_HOME and run the following command to start JRuyi.

```bash
$ bin/ruyi
```

You should see something printed similar as follows.

```xml
21:28:37.190 INFO  [Ruyi] Start JRuyi (version=2.4.1)
    ...
21:28:37.191 INFO  [Ruyi] Instance Name: default
    ...
21:28:37.238 INFO  [BootLoader] Loading bundles...
    ...
21:28:37.437 INFO  [BootLoader] Done loading bundles
21:28:37.658 INFO  [ExecutorService] Activating ExecutorService...
21:28:37.663 INFO  [ExecutorService] ExecutorService activated: {corePoolSize=16, maxPoolSize=32, keepAliveTimeInSeconds=10, queueCapacity=8192, terminationWaitTimeInSeconds=60}
21:28:37.684 INFO  [Scheduler] Scheduler activated: numberOfThreads=1
21:28:37.706 INFO  [MessageQueue] MessageQueue activated
21:28:37.788 INFO  [Provisioner] Start provisioning...
    ...
21:28:37.842 INFO  [Provisioner] Done provisioning
21:28:37.871 INFO  [BufferFactory] BufferFactory: unitCapacity=8192
21:28:37.873 INFO  [ChannelAdmin] Activating ChannelAdmin...
21:28:37.885 INFO  [SelectorThread] jruyi-selector-0 started
21:28:37.886 INFO  [ChannelAdmin] ChannelAdmin activated
21:28:37.886 INFO  [SelectorThread] jruyi-selector-1 started
21:28:37.889 INFO  [TcpAcceptor] Starting TcpAcceptor...
21:28:37.889 INFO  [TcpAcceptor] TcpAcceptor started
21:28:37.892 INFO  [TcpServer] Starting TcpServer[jruyi.clid]...
21:28:37.896 INFO  [TcpServer] TcpServer[jruyi.clid] started, listening on /127.0.0.1:6060
21:28:37.897 INFO  [ExecutorService] ExecutorService updated: {corePoolSize=16, maxPoolSize=32, keepAliveTimeInSeconds=10, queueCapacity=8192, terminationWaitTimeInSeconds=60}
```

The last line means that the JRuyi clid which is itself implemented using JRuyi IO framework, is up, and the JRuyi shell is ready to use.

Please open another console, go to $JRUYI_HOME and run the following command

```bash
$ bin/ruyi-cli
```

to enter the shell of JRuyi.

```xml
    _ _ __           _
   | | '_ \_  _ _  _(_)
 __| |   _| || | || | |
|___/|_|\_\____|\__ |_|
                |__/

JRuyi (2.4.1)
http://www.jruyi.org/

Enter 'help' for a list of available commands.
Enter 'help <command>' for more information on a specific command.
Enter 'quit' or 'exit' to exit.


localhost:6060>
```

In the mean time, you should see the following similar log is printed on the console in which JRuyi was started.

```xml
21:31:35.452 DEBUG [TcpServer] TcpServer[jruyi.clid] Session#1: OPENED
```

This log means the ruyi-cli established a TCP connection to JRuyi clid for communication.

Next, please enter `help` to get a list of available commands,

```xml
localhost:6060> help
bundle:inspect     bundle:install     bundle:list        bundle:refresh
bundle:start       bundle:stop        bundle:uninstall   bundle:update
conf:create        conf:delete        conf:exists        conf:list
conf:update        io:list            io:start           io:stop
jruyi:echo         jruyi:gc           jruyi:grep         jruyi:help
jruyi:shutdown     jruyi:sysinfo      route:clear        route:delete
route:list         route:set          scr:config         scr:disable
scr:enable         scr:info           scr:list           tpe:info
tpe:profiling
localhost:6060>
```

Enter `bundle:list` to see a list of installed bundles.

```xml
localhost:6060> bundle:list
START LEVEL 12
[ ID ][  State  ][Level][     Name     ]
    0     Active    0    org.apache.felix.framework-5.4.0
    1     Active    1    org.jruyi.osgi.log-2.0.2
    2     Active    2    org.apache.felix.metatype-1.1.2
    3     Active    2    org.apache.felix.configadmin-1.8.8
    4     Active    3    org.apache.felix.scr-2.0.2
    5     Active    6    org.jruyi.common-2.4.0
    6     Active    6    org.jruyi.tpe-2.0.2
    7     Active    6    org.jruyi.me-2.0.4
    8     Active    6    org.jruyi.io-2.3.4
    9     Active   10    org.apache.felix.gogo.runtime-0.16.2
   10     Active   10    org.jruyi.cmd-2.0.4
   11     Active   10    org.jruyi.clid-2.3.3
localhost:6060>
```

Enter `conf:list` to see configurations.

```xml
localhost:6060> conf:list
    jruyi.clid: {bindAddr=localhost, port=6060, sessionIdleTimeoutInSeconds=300}
    jruyi.common.scheduler: {numberOfThreads=1, terminationWaitTimeInSeconds=60}
    jruyi.tpe: {keepAliveTimeInSeconds=10, queueCapacity=8192, terminationWaitTimeInSeconds=60}
    jruyi.me.mq: {msgTimeoutInSeconds=10}
    jruyi.io.channeladmin: {numberOfSelectorThreads=0, numberOfIoThreads=0, capacityOfIoRingBuffer=4096}
localhost:6060>
```

Now, lets enter `exit` to exit the JRuyi shell.

```xml
localhost:6060> exit
```

And you should see the following similar log is printed on the console in which JRuyi was started.

```xml
21:35:30.359 DEBUG [TcpServer] TcpServer[jruyi.clid] Session#1: CLOSED
```

This log says ruyi-cli is done the communication with JRuyi clid and closed the TCP connection.

To stop JRuyi, simply press ctrl-c in the console in which JRuyi was started.
Alternatively, you can run the following command under $JRUYI_HOME.

```bash
$ bin/ruyi-cli shutdown
```

And you will get the following similar logs printed on the console in which JRuyi was started.

```xml
21:37:25.038 INFO  [Ruyi] Stopping JRuyi...
21:37:25.044 INFO  [TcpServer] Stopping TcpServer[jruyi.clid]...
21:37:25.044 INFO  [TcpServer] TcpServer[jruyi.clid] stopped
21:37:25.046 INFO  [TcpAcceptor] Stopping TcpAcceptor...
21:37:25.046 INFO  [TcpAcceptor] TcpAcceptor stopped
21:37:25.060 INFO  [ChannelAdmin] Deactivating ChannelAdmin...
21:37:25.060 INFO  [SelectorThread] jruyi-selector-0 stopped
21:37:25.061 INFO  [SelectorThread] jruyi-selector-1 stopped
21:37:25.061 INFO  [ChannelAdmin] ChannelAdmin deactivated
21:37:25.069 INFO  [MessageQueue] MessageQueue deactivated
21:37:25.069 INFO  [ExecutorService] Deactivating ExecutorService...
21:37:25.070 DEBUG [ExecutorService] TPE executor terminated
21:37:25.070 INFO  [ExecutorService] ExecutorService deactivated
21:37:25.074 INFO  [Scheduler] Scheduler deactivated
21:37:25.082 INFO  [Ruyi] JRuyi stopped
```

## Javadoc

Name | Version | Online | Archive
--- | --- | --- | ---
jruyi-api | 2.4.0 | [[html](http://javadoc.jruyi.org/jruyi-api/v2.4.0/)] | [[zip](https://github.com/jruyi/jruyi/releases/download/v2.4.0/jruyi-api-2.4.0-javadoc.zip)] [[tar.gz](https://github.com/jruyi/jruyi/releases/download/v2.4.0/jruyi-api-2.4.0-javadoc.tar.gz)]

## License

JRuyi is licensed under the [Apache License, Version 2.0](http://www.apache.org/licenses/LICENSE-2.0.html).