---
layout: default
title: Imperium API
parent: Imperium framework
nav_order: 2
---

# Imperium API spec

All API endpoints receive HTTP `POST` requests with json serialized data. The endpoints are as follows,

## Replica

`/replica` receives `Replica` objects. Should be called by each replica before other calls. Replicas can use this endpoint to indicate a change in the `Ready` state.

```
Replica {
    ID      string
    Ready   bool
    Info    map[string]any
    Addr    string
}
```

## Messages

`/messages` for [`Message`](/docs/basic-concepts#messages) objects. Should be called when a replica intends to send the message to another replica. Message ID's should be unique per message. Since the replicas are do not have a common counter to assign the next ID, we suggest a string encoding of the form `from_to_counter` where `from` and `to` are the ID's of the replicas and `counter` is incrementing for each new message at the sending replica.

## Events

`/events` for an `Event` object.

```
Event {
    Replica     ReplicaID
    Type        string
    Timestamp   int64
    ID          uint64
    Params      map[string]any
}
```

- `ReplicaID` is a string which should match the `ID` of the replica calling the endpoint
- `Type` to indicate the event type. Special event types are:
  - `'MessageSend'`: to indicate a message send event. There should a param key `'message_id'` where the value is the `ID` of the message that is sent
  - `'MessageReceive'`: to indicate message receive. Require the same `'message_id'` parameter.
- `Timestamp` contains the unix timestamp (seconds passed)
- `Params` can contain any additional information related to this event. The event will be logged and can later be used to inspect and diagnose.

**Imperium** expects the replica to mandatorily send `MessageSend` and `MessageReceive` events when they send a message to `/message` endpoint and when they receive a message from **Imperium** respectively. The helper [libraries](/docs/instrumentation#libraries) implement this by default. 

## Log

`/log` endpoint receives any log information that maybe helpful in later analysis. The logs are serialized in a queue that **Imperium** components can make use of. Receives `Log` objects

```
Log {
    Replica     ReplicaID
    Message     string
    Timestamp   int64
    Params      map[string]string
}
```

---
