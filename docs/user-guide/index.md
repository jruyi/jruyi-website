# User Guide

Learn more about JRuyi.

## Overview

JRuyi is built on OSGi platform which mainly uses [OSGi Declarative Services](http://wiki.osgi.org/wiki/Declarative_Services)
as the service component model itself, and uses [OSGi Configuration Admin Service](http://wiki.osgi.org/wiki/Configuration_Admin)
for configuring components.

## Architecture

<img src="/img/architecture.png" width="300" height="288" alt="Architecture"/>

JRuyi runtime consists of JRuyi Core and Thread Pool Executor

JRuyi Core consists of the following components: `Channel Admin`, `Filter Chain`, `Session Services`,
`Session Listener`, `Timer Admin` and `Scheduler`.  It provides an event-driven IO channel framework implemented in the
[Reactor pattern](http://en.wikipedia.org/wiki/Reactor_pattern) to read/write data from/to the network.
Built on this IO channel framework, there are 8 types of Session Services.  They are `TcpServer`, `UdpServer`,
`ShortConn`, `TcpClient`, `TcpClientMux`, `ConnPool`, `ConnPoolMux` and `UdpClient`.
