---
name: temporal-distributed-execution
description: Use when deploying durable, distributed, and long-running agent workflows backed by Temporal orchestrations.
---

# Temporal Distributed Execution

## Overview
Coordinates multi-agent execution, human approvals, and retryable MCP tool actions as durable Temporal workflows and activities.

## When to Use
- For workflows that may span hours or days awaiting external signals or human approvals.
- When enterprise resilience requires automatic retry with exponential backoff on transient failures.
- When execution state must survive worker crashes and process restarts without loss of progress.

## Architecture
- **Workflows**: Deterministic orchestration logic defining agent control flow and state transitions.
- **Activities**: Non-deterministic operations including LLM API inference calls and external MCP tool executions.
- **Signals & Queries**: Asynchronous external interaction primitives for human approval injection and progress inspection.
