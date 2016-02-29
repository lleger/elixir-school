---
layout: page
title: OTP Supervisors
category: advanced
order: 6
lang: en
---

Supervisors are specialized processes with one purpose: monitoring other processes. Supervisors enable us to create fault-tolerant applications by automatically restarting child processes when they fail.

## Table of Contents

- [Configuration](#configuration)
  - [Strategies](#strategies)
  - [Nesting](#nesting)
- [Task Supervisor](#task-supervisor)
  - [Setup](#setup)
  - [Supervised Tasks](#supervised-tasks)

## Configuration

The magic to Supervisors is in the `Supervisor.start_link/2` function.  In addition to starting a supervisor and its children, the `start_link/2` function allows us to define the strategy a supervisor uses for managing child processes.

Children are defined using a list and the `worker/3` function imported from `Supervisor.Spec`.  The `worker/3` function takes a module, arguments, and a set of options.  Under the hood `worker/3` calls `start_link/3` with our arguments during initialization.

Using the SimpleQueue from the [OTP Concurrency lesson](/lessons/advanced/otp-concurrency), let's get started:

```elixir
import Supervisor.Spec

children = [
  worker(SimpleQueue, [], [name: SimpleQueue])
]

{:ok, pid} = Supervisor.start_link(children, strategy: :one_for_one)
```

If our process were to crash or be terminated our Supervisor would automatically restart it as if nothing had happened.

### Strategies

There are currently four different restart strategies available to supervisors:

+ `:one_for_one` - Only restart the failed child process.

+ `:one_for_all` - Restart all child processes in the event of a failure.

+ `:rest_for_one` - Restart the failed process and any process started after it.

+ `:simple_one_for_one` - Best for dynamically attached children. Supervisor is required to contain only one child.

### Nesting

In addition to worker processes, we can also supervise supervisors to create a supervisor tree.  The only difference is swapping `supervisor/3` for `worker/3`:

```elixir
import Supervisor.Spec

children = [
  supervisor(ExampleApp.ConnectionSupervisor, [[name: ExampleApp.ConnectionSupervisor]]),
  worker(SimpleQueue, [[], [name: SimpleQueue]])
]

{:ok, pid} = Supervisor.start_link(children, strategy: :one_for_one)
```

## Task Supervisor

Tasks have their own specialized Supervisor: `Task.Supervisor`.  Designed for dynamically created tasks, the supervisor uses `:simple_one_for_one` under the hood.

### Setup

Including the `Task.Supervisor` is no different than other supervisors:

```elixir
import Supervisor.Spec

children = [
  supervisor(Task.Supervisor, [[name: ExampleApp.TaskSupervisor]]),
]

{:ok, pid} = Supervisor.start_link(children, strategy: :one_for_one)
```

### Supervised Tasks

With the supervisor started we can use the `start_child/2` function to create a supervised task:

```elixir
{:ok, pid} = Task.Supervisor.start_child(ExampleApp.TaskSupervisor, fn -> background_work end)
```

If our task crashes prematurely it will be restarted for us.  This can be particularly useful when working with incoming connections or processing background work.
