---
layout: default
title: Conditions and Actions
parent: Unit Tests
nav_order: 3
---

# Conditions and Actions
{: .no_toc}

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## Conditions

`Condition` is defined as a function,

```
type Condition func(*Event, *Context) bool
```

The function should return `true` is the condition encoded is satisfied based on the current event. Naturally, boolean connectives allow you to define more complex conditions,

```
Condition.And(Condition)
Condition.Or(Condition)
Condition.Not()
```

Other than boolean connective, some common conditions that you can use are,

| `IsMessageSend` | true if the event type is `MessageSendEventType` |
| `IsMessageReceive` | true if the event type is `MessageReceiveEventType` |
| `IsEventType(t)` | true if the event type is `GenericEventType` with `T` parameter same as the argument `t` |
| `IsMessageType(t)` | true if the event is a message send/receive with message type as `t` |
| `IsMessageFrom(from)` | true if the event is a message send/receive with `message.From == from` |
| `IsMessageTo(to)` | true if the event is a message send/receive with `message.To == to` |

## Actions

`Action` should be used to determine the consequence of an `If().Then()` handler.

```
type Action func(*Event, *Context) []*Message
```

Default `Actions` are:

| `DeliverMessage` | If the event is a message send, returns the respective message from the pool |
| `DropMessage` | Returns an empty Message set. Should be used for better code expressivity |
| `RecordMessageAs(label)` | If the event is a message send/receive, records the message in `Context.VarSet` with the specified `label` |

## Message sets

The `RecordMessageAs` action indicates a need to record events which are messages and later use them. We abstract this concept to define `MessageSet` which can be used to store messages. Sets are referenced by a label and stored as part of `Context.VarSet`. To access `MessageSet`, a set of conditions and actions are provided:

| `Set(label).Store()` | Actions to store the message. This is possible only if the event is message send/receive. `label` references the message set |
| `Set(label).DeliverAll()` | Action that returns all the messages in the set referenced by `label`. It removes all the messages from the set |
| `Set(label).Contains()` | Condition that returns true if the event is a message send/receive and the message is part of the set referenced by `label` |

## Counters

Apart from storing messages in a set, counting common events is a common theme when describing handlers. We define `Counter` which stores an integer counter as part of the `Context.VarSet`. 
`Counter` is also referenced by a `label`. One way to access counter is using `Count(label)`, but in those circumstances when the `label` is dynamic, we allow you to specify a function which returns the label `CountF(labelFunc)` where `labelFunc(*Event, *Context) string` returns the label based on the current event.

Similar to message set, we provide conditions and actions to access and manipulate counter

| `Count(label).Incr()` | Action to increment a counter referenced by `label` |
| `Count(label).LtF(valF)`, `Count(label).Lt(val)` | Condition functions to check if the counter is less than the value specified. `LtF` allows you to fetch the value based on the current event and context |
| `Count(label).GtF(valF)`, `Count(label).Lt(val)` | Condition that returns true if the counter is greater than value |
| `Count(label).GeqF(valF)`, `.Geq(val)`, `.LeqF(valF)`, `.Leq(val)` | Condition functions to assert the counter value |

Additionally, `Set(label).Count()` is a default counter available for every message set defined.