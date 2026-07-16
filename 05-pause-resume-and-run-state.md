# Pause, Resume, and Run State

## Overview

A workflow run in Dify keeps both a run record and per-node execution records. `WorkflowPersistenceLayer` listens to `GraphEngine` events and writes those records as execution moves forward, so persistence stays attached to the engine rather than to node code. For the broader shape of a run, see [01 Anatomy of a Workflow Run](/01-anatomy-of-a-workflow-run.md) and [02 Inside the Graph Engine](/02-inside-the-graph-engine.md).

## Run records

`WorkflowRun` captures the run identity, trigger source, graph snapshot, inputs, outputs, error text, elapsed time, token and step counts, creator identity, and start and finish timestamps. `WorkflowNodeExecutionModel` captures the same story at node scope: node identity, predecessor, inputs, process data, outputs, status, error, elapsed time, execution metadata, and timestamps.

The persistence layer creates the workflow row on `GraphRunStartedEvent`, then updates it on `GraphRunSucceededEvent`, `GraphRunPartialSucceededEvent`, `GraphRunFailedEvent`, `GraphRunPausedEvent`, and `GraphRunAbortedEvent`. It saves node rows on node start, retry, success, failure, exception, and pause-request events.

## Pause

The `human_input` path pauses the graph at the engine level. When `GraphRunPausedEvent` arrives, `PauseStatePersistenceLayer` stores a `WorkflowResumptionContext` with the graph runtime snapshot, the generate entity, and the response stream filter state, then writes the workflow pause record with the mapped pause reasons. Dify stores that snapshot in its workflow pause row, and human-input form records keep the token and run binding that let the pause survive across requests.

The snapshot matters because it records the exact execution position. It also preserves the filter state that the live response stream uses, so a later consumer can continue from the same stream shape instead of starting over.

## Resume

A paused run resumes in a later process, not in the original request. `HumanInputService.submit_form_by_token(...)` marks the form as submitted and enqueues `resume_app_execution`, and the resume task loads the pause row, decodes `WorkflowResumptionContext.loads(...)`, rebuilds `GraphRuntimeState.from_snapshot(...)`, and restores the response stream filter before it calls the workflow generator again.

That path continues from the stored engine state rather than replaying the whole graph. Completed nodes stay completed, node execution history stays attached to the run, and the resumed process advances only from the pause point forward.

## Timeslicing

`TimeSliceLayer` adds a scheduler check around workflow execution. When the plan uses `WorkflowScheduleCFSPlanEntity.Strategy.TimeSlice`, the layer polls the scheduler, and `RESOURCE_LIMIT_REACHED` turns into a `PAUSE` command. The workflow pauses for capacity reasons instead of failing.