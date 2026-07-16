# About this site

This field guide explains how the Dify codebase fits together. It exists to give engineers a working mental model of the product surface, especially the parts that are easier to understand from source than from user facing documentation.

The guide does not replace the official documentation. It complements [docs.dify.ai](https://docs.dify.ai) by focusing on platform internals, execution paths, and the relationships between the main code areas.

## Who this is for

This guide is for engineers who adopt, operate, extend, or contribute to Dify and need to understand how requests move through the platform, how workflow execution reaches the graph runtime, and where the moving pieces live. It assumes the reader can read code but wants the map before tracing symbols and call paths.

## How this site was made

Doc Holiday wrote this site by exploring `langgenius/dify` and `langgenius/graphon` directly. The pages come from real paths and symbol names in `api/` and `src/graphon/`, not from product copy. The result reflects how the code actually fits together: Dify’s integration layer in `api/` and Graphon’s workflow engine and runtime internals in `src/graphon/`.

## Scope and current state

This guide reflects a snapshot of a very active codebase. As of July 2026, the agent stack and CLI change faster than the main `api/` plus `src/graphon/` path, so the guide treats those areas as brief observations rather than fixed reference material. The official documentation at [docs.dify.ai](https://docs.dify.ai) remains the authoritative source for product behavior, setup, and operations, while this site focuses on the internals those docs do not explain.

## Table of contents

| Page | Topic |
| --- | --- |
| [01-anatomy-of-a-workflow-run.md](01-anatomy-of-a-workflow-run.md) | How a workflow run moves from request to result. |
| [02-workflow-entry-and-app-runner.md](02-workflow-entry-and-app-runner.md) | How Dify starts graph execution and routes engine events. |
| [03-dify-to-graphon-adapters.md](03-dify-to-graphon-adapters.md) | How Dify wraps graphon tools, nodes, and runtime helpers. |
| [04-graph-runtime-state.md](04-graph-runtime-state.md) | How graphon tracks execution, suspension, and serialization. |
| [05-iteration-nodes.md](05-iteration-nodes.md) | How child graphs run over ordered items and combine results. |
| [06-human-input-and-file-references.md](06-human-input-and-file-references.md) | How runs pause for people and file backed context. |
| [07-agent-stack.md](07-agent-stack.md) | The fast moving agent surface and its place in the system. |
| [08-cli-and-operational-surface.md](08-cli-and-operational-surface.md) | The command line and support tooling around the platform. |

## Where to look in the code

- `langgenius/dify/api/` for the platform integration layer and workflow entry points.
- `langgenius/graphon/src/graphon/` for runtime state, node execution, iteration, and suspension handling.
- The same split applies throughout the guide: Dify owns the product surface, while Graphon owns the execution engine.

## Source note

- Generated from: [GENERATED_FROM: commit SHORT_SHA, DATE — the operator will replace this placeholder; include it verbatim if you cannot determine it]
- Repository or contact: [CONTACT_OR_REPO_LINK placeholder for the operator]