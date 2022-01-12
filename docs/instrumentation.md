---
layout: default
title: Instrumentation
nav_order: 3
permalink: /docs/instrumentation
---

# Instrumentation

In order to gain control over the distributed system, **Imperium** requires

1. Replicas to communicate messages and events through it. The send and receive primitives of the replicas should now communicate with **Imperium**.
2. Ability to start, stop or reset replicas. **Imperium** sends `Directive` messages to achieve this. Replicas should perform the requested action when they receive a `Directive` message.

## Libraries

To aid this process, we have language specific libraries. Currently available for [`go`](https://github.com/ImperiumProject/go-replicaclient).

## Directives

`Directive` messages are used by **Imperium** to instruct replicas to start, stop or reset. Message structure contains just the action
```
DirectiveMessage {
    Action string
}
```

## Replica API spec

Each replica should run a HTTP server and publish the address of that server to **Imperium**. The API endpoints are

1. `/message` receives the [`Message`](/docs/basic-concepts#messages) object serialized as json in a `POST` request. Should respond with status `200` to acknowledge message reception.
2. `/directive` receives the `DirectiveMessage` serialized as json in a `POST` request. Should respond with status `200` after completing the requested action.
3. `/health`: `GET` request is called to check the availability of the replica. Should respond with `200` to confirm availability.

## Communicating with Imperium

Replicas should persist a HTTP connection to **Imperium** API endpoint. This connection should be used to send replica information, events and messages. See [API documentation]({% link docs/framework/api.md %}) for details about API.

Before any request is made, replica should call the `/replica` API endpoint
with the following information

```
Replica {
    ID      string
    Ready   bool
    Info    map[string]any
    Addr    string
}
```

- `Ready` is used to indicate if the replica is ready to send and receive messages. **Imperium** will wait for all replicas to be ready before continuing. 
- `Addr` should be Replica API endpoint that **Imperium** will use to communicate messages and directives.
- `Info` can be any additional information. This information can be used when writing unit tests. For example, replicas can submit their private keys such that unit tests can change the contents of messages originating from this replica.

Following this call the replica should use the same `ID` when sending messages and events.