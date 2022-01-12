---
layout: default
title: Imperium framework
has_children: true
nav_order: 7
permalink: /docs/framework
has_toc: false
---

# Framework documentation

**Imperium** provides the basic building blocks to enable central monitoring and control of replicas in a distributed system. With this central control, the framework aims to implement testing and visualization of the system. **Imperium** is modular to allow all class of distributed systems and protocol to be plugged in and is extensible in the form of control that can be exercised. Refer []

## Configuration

A global configuration object provides the details needed for **Imperium** to run. Configuration consists of,

```
Config {
    NumReplicas     int
    APIServerAddr   string
    LogConfig {
        Path    string
        Format  string
        Level   string
    }
}
```

### LogConfig

- `Path` is used to log to a file. If unspecified, **Imperium** logs to `stdout`. 
- `Format`: Valid values are `json`. If left empty, **Imperium** logs in text format
- `Level`: Valid values are `panic,fatal,error,warn,warning,info,debug,trace` signifying the log level

## Context

`RootContext` object serves the backbone of the framework. To run any components, one needs to pass a `RootContext` instance. The context contains the necessary queues and stores that components can make use of.

- `ctx.ReplicaStore`: thread safe key value store of `Replica` information
- `ctx.MessageStore`: thread safe message pool indexed by the message `ID`
- `ctx.EventQueue`: thread safe event queue that components can subscribe to.
- `ctx.Config`: reference to the configuration object
- `ctx.Logger`: A generic logger that components use to instantiate component specific logger objects

[`APIServer`](#APIServer) uses the `RootContext` object to push information about messages, events and replicas.

## Components

The main components of **Imperium** are as follows

### APIServer

`APIServer` instantiates a HTTP server at the configured `APIServerAddr` and implements the [API spec]({% link docs/framework/api.md %}). `APIServer` requires a `MessageParser` as an input parameter. `MessageParser` object is specific to the protocol being tested and allows a developer to plug in the deserializing and serializing of messages received on the [`/message`](/docs/framework/api#message) endpoint.

`APIServer` also hosts a dashboard that can be used to provide a user interface for interactivity. `DashboardRouter` interface allows the mode used to provide UI specific APIs that can be used to control the components and measure metrics

### Dispatcher

`Dispatcher` is used to send messages and directives to the replica. Instantiated with a context object, the `Dispatcher` communicates with the replicas in the [Replica API spec](/docs/instrumentation#replica-api-spec).
