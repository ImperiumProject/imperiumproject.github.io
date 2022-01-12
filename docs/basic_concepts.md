---
layout: default
title: Basic concepts
nav_order: 2
permalink: /docs/basic-concepts
---

# Basic Concepts

Following are basic concepts from distributed systems. They form the foundation needed to understand and use **Imperium**.

## Replicas/Nodes

Replicas are individual servers/processes that are connected to other replicas.

## Messages

Replicas communicate with each other by sending messages. There can be many types of messages defined according to the protocol. Messages are defined by 
```
Message {
    ID      string
    From    Replica
    To      Replica
    Data    []byte
    Type    MessageType
}
```

## Timers

Stopwatches that are running at each replica. Timers can be set or reset.

## Distributed protocol

An algorithm that is run at every replica. The algorithm defines the rules of engagement between replicas. Protocols define

- Replies for a message
- Rules to set a timer
- Rules to broadcast a message
- ...

## Clients

Clients are replicas that do not take part in the protocol. They only send requests to one or more replicas and receive responses.

## Events

Events represent a computation at each replica. During the execution of a protocol, replicas go through a sequence of events. Events can be timer start, timer end, message received ...

Event is defined by 
```
Event {
    ID          string
    Replica     Replica
    Type        EventType 
    Timestamp   Time
}
```

## Event ordering

Events have a natural partial order. We say Event `a` happens before `b` if

1. `a` happens before `b` in the same replica
2. `a` is a message send and `b` is the corresponding message receive

## Faults

Faults can be of two types. Benign or byzantine,

**Benign**: Unintentional faults on the network or on individual replicas. A replica can crash/stop, stop responding to messages. Similarly, network can drop messages, delay messages or duplicate messages.

**Byzantine**: Intentional faults by replicas. Deviations from the protocol. Replicas can lie, send different messages to different replicas or collude with other byzantine replicas.

## Execution

Replicas execute the protocol after an initial trigger (a client request), and Messages are exchanged between replicas. The events of the replicas along with the happens before ordering acts as a snapshot of this execution. An execution is then `H = (E, <)` where `E` is the set of events and `<` is the happens before relation.

---
