---
layout: default
title: Unit Tests
nav_order: 4
has_children: true
permalink: /docs/testing
---

# Unit testing

{: .no_toc}

Traditional unit testing involves describing scenarios and expected outcome for specific parts of the code. The framework runs these tests and reports the outcomes which can be used to find and fix bugs. We translate this idea to the distributed protocol world. **Imperium** allows a developer to describe scenarios programmatically and specify the expected outcomes.

## Restrictions as scenarios

To describe scenarios, consider an implementation of the following protocol. Each replica sends pings to all its neighbors at regular intervals. If a replica does not receive a ping from a known neighbor after a timeout, it marks the link as down and does not send any more pings. 

Now, we would like to test if the implementation correctly marks a link as down. This would require that a replica `A` does not receive pings from its neighbor replica `B` long enough to mark the link as down. To simulate this with **Imperium** you can specify a restriction on the messages as follows,

```
If(MessageFrom(B).And(MessageTo(A))).Then(DropMessage)
```

Subsequently, you can assert that replica `A` publishes an event that the link was marked as down. Since **Imperium** receives all messages between replicas, we can allow the developer to define restrictions on which messages should be delivered in what order. A set of these restrictions along with an assertion describe a unit test.

## Assertion

In the above example, we expect that replica `A` publishes an event announcing that the link was marked as down. In **Imperium**, this check can be described as a state machine:

```
sm := NewStateMachine()
initial := sm.Builder()
initial.On(LinkDown(A, B), "Success")
```

## Library for describing unit tests

The above example is meant to give an idea of unit testing in the distributed context. Since unit tests are specific to the implementation and protocol being testing, **Imperium** exports [`testlib`](https://pkg.go.dev/github.com/ds-test-framework/scheduler@v1.9.4/testlib) as a `go` library which can be used to describe unit tests. This allows developers to maintain unit tests as part of their implementation codebase.

Refer [Library Usage]({% link docs/tests/testlib.md %}) for documentation related to using the library and [Writing Tests]({% link docs/tests/writing_tests.md %}) for documentation about writing unit tests.