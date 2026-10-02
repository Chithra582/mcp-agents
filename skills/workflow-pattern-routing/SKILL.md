---
name: workflow-pattern-routing
description: Use when constructing multi-agent architectures using routing, orchestrator-worker, parallelization, or evaluator-optimizer patterns.
---

# Workflow Pattern Routing

## Overview
Deploys composable agent patterns to solve complex, multi-stage reasoning tasks through coordinated sub-agents and deterministic routing.

## When to Use
- When incoming queries must be dispatched to specialized domain experts (coding, search, math, documentation).
- When a master orchestrator needs to decompose tasks into sub-tasks and aggregate parallel worker outputs.
- When outputs require iterative refinement through a critique and evaluation loop.

## Workflow Archetypes
1. **Router**: Classifies task intent and routes to a single specialized sub-agent.
2. **Parallel Fan-Out**: Dispatches independent sub-tasks across concurrent agents and synthesizes results.
3. **Orchestrator-Workers**: Dynamic planning agent breaks down objectives and coordinates worker agents.
4. **Evaluator-Optimizer**: Two-agent loop where an evaluator tests and critiques generator outputs until quality criteria are satisfied.

## Best Practices
- Keep sub-agent prompt contexts clean by injecting only task-relevant state.
- Set explicit iteration ceilings for evaluator-optimizer loops to guarantee termination.
