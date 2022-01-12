---
layout: default
title: Library usage
parent: Unit Tests
nav_order: 1
---

# Library Usage

The unit testing framework is exported as [`testlib`](https://pkg.go.dev/github.com/ds-test-framework/scheduler@v1.9.4/testlib) library. To invoke **Imperium** you need to instantiate `TestingServer` as follows,

```
server := testlib.NewTestServer(
    config,
    parser,
    []*TestCase{
        testCaseOne,
        testCaseTwo,
        ...
    }
)
```

- `config` is an instance of the [Configuration](/docs/framework#configuration) object.
- `parser` is an implementation of the [`MessageParser`](https://pkg.go.dev/github.com/ds-test-framework/scheduler@v1.9.4/types#MessageParser) interface
- A list of `TestCase` objects which represent the unit tests that one needs to run.

To run the unit tests, call `server.Start()`. `server.Done()` returns a channel which will close once all the unit tests are executed. `server.Stop()` will terminate the server.