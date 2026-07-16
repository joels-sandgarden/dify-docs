# Pause, Resume, and Run State

## Overview

A workflow run in Dify keeps both a run record and per-node execution records. `WorkflowPersistenceLayer` listens to `GraphEngine` events and writes those records as execution moves forward, so persistence stays attached to the engine rather than to node code. For the broader shape of a run, see [01 Anatomy of a Workflow Run](/01-anatomy-of-a-workflow-run.md) and [02 Inside the Graph Engine](/02-inside-the-graph-engine.md).

## Run records

`WorkflowRun` captures the run identity, trigger source, graph snapshot, inputs, outputs, error text, elapsed time, token and step counts, creator identity, and start and finish timestamps. `WorkflowNodeExecutionModel` captures the same story at node scope: node identity, predecessor, inputs, process data, outputs, status, error, elapsed time, execution metadata, and timestamps.

The persistence layer creates the workflow row on `GraphRunStartedEvent`, then updates it on `GraphRunSucceededEvent`, `GraphRunPartialSucceededEvent`, `GraphRunFailedEvent`, `GraphRunPausedEvent`, and `GraphRunAbortedEvent`. It saves node rows on node start, retry, success, failure, exception, and pause-request events.