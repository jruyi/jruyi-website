# Getting Started

Some examples to let you get started quickly with JRuyi.

To get the source code of examples, please check [jruyi-examples](https://github.com/jruyi/jruyi-examples) on Github.
In the later sections, **$JRUYI_EXAMPLES_HOME** will be used to refer to the root directory of your local copy of the repo
`jruyi-examples`.  All the example projects are available in Gradle as well as Maven.

!!! note "Note:"
    All the examples are given under a UNIX-like environment. It should be very straightforward for you to make them
    work on Windows. Just use the counterpart BAT file for a shell script file mentioned in the examples. For example,
    use ruyi.bat for ruyi and ruyi-cli.bat for ruyi-cli.

We also assume that you know how to download JRuyi.  If you don't, please go check [Download](/#download). And we will
use **$JRUYI_HOME** to refer to the directory where the downloaded jruyi package is unpacked.

## Building a Discard Server

Lets start with writing a [discard](http://tools.ietf.org/html/rfc863 "Discard Protocol") server which simply throws away any received data.

To implement a discard server, simply create an [INioService](http://javadoc.jruyi.org/jruyi-api/v2.3.1/org/jruyi/core/INioService.html)
of type TcpServer, and start it as the following code shows.

```java
package org.jruyi.example.discard;

import org.jruyi.core.INioService;
import org.jruyi.core.ITcpServerConfiguration;
import org.jruyi.core.RuyiCore;
import org.jruyi.io.IBuffer;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class DiscardServer {

	private static final Logger c_logger = LoggerFactory.getLogger(DiscardServer.class);

	public static void main(String[] args) {
		try {
			// Build an Nio Service of type TcpServer
			final INioService<IBuffer, Object, ? extends ITcpServerConfiguration> tcpServer = RuyiCore
					.newTcpServerBuilder()
					.port(10009)
					.serviceId("jruyi.example.discard")
					.build();

			// Start tcpServer
			tcpServer.start();
		} catch (Throwable t) {
			c_logger.error("Failed to start discard server", t);
		}
	}
}
```
Simple? Yeah, it is.  But there's a problem of this code that it cannot serve as a good demo.  Because there's no prove
that we actually got the data sent from a client.  So lets write a session listener and log the received data.

```java
package org.jruyi.example.discard;

import org.jruyi.common.StrUtil;
import org.jruyi.io.IBuffer;
import org.jruyi.io.ISession;
import org.jruyi.io.SessionListener;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

class DiscardServerListener extends SessionListener<IBuffer, Object> {

	private static final Logger c_logger = LoggerFactory.getLogger(DiscardServerListener.class);

	@Override
	public void onMessageReceived(ISession session, IBuffer inMsg) {
		c_logger.info(StrUtil.join("Got data >>", StrUtil.getLineSeparator(), inMsg));
	}
}
```

Method `DiscardServerListener.onMessageReceived` will be invoked when any data arrives. And it dumps the received data.

Next, lets hook this DiscardServerListener to the NioService before starting.

```java
...
// Set sessionListener
tcpServer.sessionListener(new DiscardServerListener());

// Start tcpServer
tcpServer.start();
...
```

Now, you should/can see what data the server gets.  However, this code is still not good enough.  You may have already
noticed that there's a `tcpServer.start()` but no call of stop, which means the NioService does not stop gracefully when this
application terminates.  What should we do then?  I'm not going to discuss the solution here since it does not really
relate to JRuyi.  You can find the solution in the source code
[DiscardServer.java](https://github.com/jruyi/jruyi-examples/blob/master/discard/src/main/java/org/jruyi/example/discard/DiscardServer.java).

If you have git-cloned the [jruyi-examples](https://github.com/jruyi/jruyi-examples) from Github, please go to the directory
**$JRUYI_EXAMPLES_HOME**/discard.  Then build the project by running the following command.

```bash
$ ./gradlew clean build
```

If building successfully, you should get the jar locating at **$JRUYI_EXAMPLES_HOME**/discard/build/libs, and you can start
the discard server as follows.

```bash
$ java -jar build/libs/discard-1.0.0-SNAPSHOT.jar
```

!!! Note "For Maven Users:"
    Please use the following command to build the project.

    ```
        $ mvn clean package
    ```

    And use the following command to run the server.

    ```
        $ java -jar target/discard-1.0.0-SNAPSHOT.jar
    ```

You should see logs printed on console similar as follows.

```xml
[main] INFO org.jruyi.common.internal.Scheduler - Scheduler activated: numberOfThreads=1
[main] INFO org.jruyi.io.channel.ChannelAdmin - Activating ChannelAdmin...
[jruyi-selector-0] INFO org.jruyi.io.channel.SelectorThread - jruyi-selector-0 started
[main] INFO org.jruyi.io.channel.ChannelAdmin - ChannelAdmin activated
[jruyi-selector-1] INFO org.jruyi.io.channel.SelectorThread - jruyi-selector-1 started
[main] INFO org.jruyi.io.buffer.BufferFactory - BufferFactory: unitCapacity=8192
[main] INFO org.jruyi.io.tcpserver.TcpAcceptor - Starting TcpAcceptor...
[main] INFO org.jruyi.io.tcpserver.TcpAcceptor - TcpAcceptor started
[main] INFO org.jruyi.io.tcpserver.TcpServer - Starting TcpServer[jruyi.example.discard]...
[main] INFO org.jruyi.io.tcpserver.TcpServer - TcpServer[jruyi.example.discard] started, listening on /0:0:0:0:0:0:0:0:10009
```

OK, we just got the discard server started. It's time to use telnet to make a test.

```xml
$ telnet localhost 10009
```

Then just type something and you won't get any response.
But you should see what you typed be dumped on the console in which the discard server started.

Congratulations! You just learned how to use the lib `jruyi-core` to build a discard server.  But it would be much easier
if you use `jruyi` runtime.  Let me show you how easy it is.

#### 1. Start JRuyi

Please go to **$JRUYI_HOME** and start JRuyi as follows.

```bash
$ bin/ruyi
```

#### 2. Create a Session Service Endpoint

Open a new console and run the following command under **$JRUYI_HOME** to create a TCP server (Session Service) listening on port 10009.

```xml
$ bin/ruyi-cli conf:create jruyi.me.endpoint.tcpserver jruyi.me.endpoint.id=jruyi.example.discard port=10009
```

The created TCP Server Endpoint is identified with `jruyi.example.discard`.

As you see, a Session Service Endpoint of TcpServer is created by creating a configuration of `jruyi.me.endpoint.tcpserver`.

#### 3. Configure the Routing Table

Run the following command under **$JRUYI_HOME** to set a route.

```xml
$ bin/ruyi-cli route:set jruyi.example.discard jruyi.me.endpoint.null
```

This is to tell Messaging Engine to dispatch any messages from endpoint `jruyi.example.discard` to endpoint `jruyi.me.endpoint.null` which is a builtin endpoint to swallow any messages dispatched to it.

That's it! You just built a discard server using JRuyi and you can use telnet to make tests.

To see the data that the discard server received, you can add the builtin MsgLog Filter to the filter chain of the TcpServer `jruyi.example.discard` by running the following command under **$JRUYI_HOME**.

```xml
$ bin/ruyi-cli conf:update '"(jruyi.me.endpoint.id=jruyi.example.discard)"' filters=jruyi.io.msglog.filter
```

Now use telnet to test again.  You should see what you typed be dumped on the console in which JRuyi was started.

#### 4. Shutdown JRuyi

To shutdown JRuyi, simply press ctrl-c in the console in which JRuyi was started. Alternatively, you can run the following command under **$JRUYI_HOME**.

```bash
$ bin/ruyi-cli shutdown
```

## Building an Echo Server

In [Building a Discard Server](#building-a-discard-server), you should have got the idea of how to receive data from
client in JRuyi. But you might not be clear on how to send back some data to client. So next, let's show you the how-to
by building an [echo](http://tools.ietf.org/html/rfc862 "Echo Protocol") server.

The key difference to implement an echo server is that the SessionListener need hold a reference to the `INioService`.
And use it to send back whatever is received in method `onMessageReceived`.

```java
package org.jruyi.example.echo;

import org.jruyi.core.INioService;
import org.jruyi.core.ITcpServerConfiguration;
import org.jruyi.io.IBuffer;
import org.jruyi.io.ISession;
import org.jruyi.io.SessionListener;

class EchoServerListener extends SessionListener<IBuffer, IBuffer> {

	// Hold a reference to INioService
	private final INioService<IBuffer, IBuffer, ? extends ITcpServerConfiguration> m_tcpServer;

	EchoServerListener(INioService<IBuffer, IBuffer, ? extends ITcpServerConfiguration> tcpServer) {
		m_tcpServer = tcpServer;
	}

	@Override
	public void onMessageReceived(ISession session, IBuffer inMsg) {
		// Send back whatever is received.
		m_tcpServer.write(session, inMsg);
	}
}
```

Class `EchoServer` is almost the same as class `DiscardServer` except that the instance of `INioService` need be used to
construct an instance of `EchoServerListener`. Below is the code for `EchoServer`.

```java
package org.jruyi.example.echo;

import org.jruyi.core.INioService;
import org.jruyi.core.ITcpServerConfiguration;
import org.jruyi.core.RuyiCore;
import org.jruyi.io.IBuffer;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class EchoServer {

	private static final Logger c_logger = LoggerFactory.getLogger(EchoServer.class);

	static class ShutdownHook extends Thread {

		private final INioService<IBuffer, IBuffer, ? extends ITcpServerConfiguration> m_tcpServer;

		ShutdownHook(INioService<IBuffer, IBuffer, ? extends ITcpServerConfiguration> tcpServer) {
			m_tcpServer = tcpServer;
		}

		@Override
		public void run() {
			m_tcpServer.stop();
		}
	}

	public static void main(String[] args) {
		try {
			// Build an Nio Service of type TcpServer
			final INioService<IBuffer, IBuffer, ? extends ITcpServerConfiguration> tcpServer = RuyiCore
					.newTcpServerBuilder()
					.port(10007)
					.serviceId("jruyi.example.echo")
					.build();

			// Set sessionListener
			tcpServer.sessionListener(new EchoServerListener(tcpServer));

			// Start tcpServer
			tcpServer.start();

			// To shutdown gracefully
			Runtime.getRuntime().addShutdownHook(new ShutdownHook(tcpServer));
		} catch (Throwable t) {
			c_logger.error("Failed to start echo service", t);
		}
	}
}
```

You can also use JRuyi runtime to build an echo server.

After starting JRuyi, please execute the following commands under **$JRUYI_HOME**.

```xml
# Create a TCP server listening on port 10007
$ bin/ruyi-cli conf:create jruyi.me.endpoint.tcpserver jruyi.me.endpoint.id=jruyi.example.echo port=10007

# Route any message from endpoint jruyi.example.echo back to itself
$ bin/ruyi-cli route:set jruyi.example.echo jruyi.example.echo
```

Now, you should be able to test echo server by running the following command.
```xml
$ telnet localhost 10007
```
This time you will see that whatever you typed will be echoed.

## Building a Daytime Server
