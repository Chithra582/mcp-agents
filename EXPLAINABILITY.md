# EXPLAINABILITY.md

This document explains the internal mechanisms, data lineage, operational boundaries, and governance framework of **MCP Agents** (`mcp-agents`) in accordance with the **OpenGAP v0.1.0** specification for the **HiDevs GitAgent Passport** clearance pipeline.

> **Agent Name:** MCP Agents (`mcp-agents`)  
> **Specification:** OpenGAP v0.1.0  
> **Category / Domain:** Developer Tools / Model Context Protocol (MCP) Multi-Agent Framework  
> **Compliance Standard:** OpenGAP Checkpoint 2 (Explainability & Decision Governance), OWASP LLM Top 10, MITRE ATLAS  

---

## How the Agent Decides

The agent operates via a strictly disciplined, 5-stage deterministic execution pipeline coordinating MCP server discovery, routing, tool invocation, human governance, and result evaluation.

### 1. Decision Architecture

The runtime intake, state classification, evaluation, and execution tracking operate across a deterministic, five-stage pipeline:

```
+-----------------------------------------------------------------------------------+
|                        Deterministic MCP Agent Pipeline                           |
+-----------------------------------------------------------------------------------+
|  [Stage 1: Intent Ingestion & Protocol Handshake Gate]                            |
|     --> Receive task prompt, verify active MCP connections, and enumerate tools   |
+-----------------------------------------+-----------------------------------------+
                                          |
                                          v
+-----------------------------------------------------------------------------------+
|  [Stage 2: Workflow Pattern Selection & Subagent Routing]                         |
|     --> Compute routing affinity; select Router, Parallel, or Orchestrator mode   |
+-----------------------------------------+-----------------------------------------+
                                          |
                                          v
+-----------------------------------------------------------------------------------+
|  [Stage 3: Tool Invocation & Risk Assessment Interception]                        |
|     --> Filter MCP tool schemas; intercept sensitive operations for human approval|
+-----------------------------------------+-----------------------------------------+
                                          |
                                          v
+-----------------------------------------------------------------------------------+
|  [Stage 4: Execution, Telemetry Tracing & Error Handling]                         |
|     --> Execute approved tools over JSON-RPC; emit OTel spans; handle retries     |
+-----------------------------------------+-----------------------------------------+
                                          |
                                          v
+-----------------------------------------------------------------------------------+
|  [Stage 5: Evaluator-Optimizer Review & Output Synthesis]                         |
|     --> Critique candidate output against rubric; finalize or loop for refinement |
+-----------------------------------------------------------------------------------+
```

### 2. Decision Logic & Routing Formulations

Routing confidence across candidate specialized sub-agents $a \in A$ for an input query $q$ is determined by a normalized multi-factor affinity scoring function:

$$S_{\text{affinity}}(a) = w_1 \cdot \text{CosineSimilarity}(\mathbf{e}_q, \mathbf{e}_a) + w_2 \cdot \text{ToolCoverage}(a, q) + w_3 \cdot \text{HistoricalSuccess}(a)$$

Where:
- $w_1 = 0.50$: Semantic embedding proximity between query intent $\mathbf{e}_q$ and agent specialization $\mathbf{e}_a$.
- $w_2 = 0.35$: Fraction of required MCP tools exposed by servers mapped to agent $a$.
- $w_3 = 0.15$: Tracked historical task completion rate for agent persona $a$.

For the Evaluator-Optimizer workflow pattern, output refinement terminates when the quality score satisfies:

$$Q_{\text{eval}}(y) = \sum_{i=1}^{M} \lambda_i \cdot r_i(y) \ge \tau_{\text{quality}}$$

Where $r_i(y) \in [0, 1]$ represents compliance with rubric criterion $i$, weights $\sum \lambda_i = 1$, and $\tau_{\text{quality}} = 0.85$.

### 3. Thresholding & Refusal Decision Criteria

MCP Agents enforces strict operational boundaries and deterministic refusal thresholds:
- **Refusal on ERR_MCP_TRANSPORT_CONNECTION_FAILED**: Transport Connectivity (Ping response > 5000 ms or connection lost) halts execution with code `ERR_MCP_TRANSPORT_CONNECTION_FAILED`.
- **Refusal on ERR_LOW_ROUTING_CONFIDENCE**: Routing Confidence ($S_{\text{affinity}} < 0.70$) halts execution with code `ERR_LOW_ROUTING_CONFIDENCE`.
- **Refusal on ERR_UNAUTHORIZED_DESTRUCTIVE_TOOL**: High-Risk Tool Operation (Write/Delete action without approval) halts execution with code `ERR_UNAUTHORIZED_DESTRUCTIVE_TOOL`.
- **Refusal on ERR_EVALUATOR_OPTIMIZER_ITERATION_CAP**: Optimization Loop Cap (Iteration count $\ge 5$) halts execution with code `ERR_EVALUATOR_OPTIMIZER_ITERATION_CAP`.
- **Refusal on ERR_HUMAN_APPROVAL_TIMEOUT**: Human Review Timeout (Operator response time > 300 s) halts execution with code `ERR_HUMAN_APPROVAL_TIMEOUT`.

### 4. Fallback Decision Mechanism

Continuous operational stability is maintained through layered fault recovery:
- **Tier 1 (Automated Transport & Tool Retry)**: Transient network errors or JSONRPC timeouts trigger up to 3 automatic retries with exponential backoff before marking an MCP server unavailable.
- **Tier 2 (Model & Workflow Rerouting)**: If a specialized subagent fails to generate a valid tool call, the router falls back to a primary orchestrator model with full tool schema context.
- **Model Fallback Cascade**: High-level reasoning and synthesis default to `gemini-2.0-flash` with automatic failover to `gpt-4o` and `claude-3-5-sonnet`.

### 5. Human-in-the-Loop Governance

Human operators retain sovereign authority over the multi-agent execution lifecycle:
- **Tier 3 (HumanintheLoop Escalation)**: Irreversible state alterations or persistent validation failures halt the workflow, presenting a structured diff to human operators for affirmative approval or manual remediation.
- **Benchmark Trajectory Auditing**: Operators inspect evaluation traces, raw generation tokens, and container logs to verify scoring fidelity.

---

## The Data It Uses

MCP Agents operates under strict principles of data minimization, environment isolation, and privacy protection.

### 1. Ingested Input Data

The framework processes only operational data necessary to perform its functions:
- **User Instructions & Prompts**: Natural language queries, task objectives, and domain parameters.
- **MCP Protocol Schemas**: Tool definitions, resource URIs, and prompt templates retrieved via JSON-RPC 2.0.
- **Telemetry & Trace Events**: OpenTelemetry trace spans, execution latencies, and tool error payloads.

### 2. Configuration & Reference Data

- **Model Context Protocol (MCP)**: Open standard for LLM-tool interoperability over stdio, SSE, and WebSocket.
- **Agent Workflow Archetypes**: Prompt Chaining, Routing, Parallelization, Orchestrator-Workers, Evaluator-Optimizer.
- **Distributed Durability**: Temporal workflow definitions and state transitions.

### 3. Base Model & Inference Lineage

- **Host LLM Providers**: Anthropic Claude, OpenAI GPT, Google Gemini, AWS Bedrock, Azure OpenAI.
- **Runtime Environment**: Python 3.10+, FastAPI, AnyIO, Uvicorn, Temporal.io Python SDK.

### 4. Data Privacy, Storage, and Retention

- **OWASP LLM & MITRE ATLAS Hardened**: Defended against indirect prompt injection, credential leakage, and unauthorized external API dispatch.
- **Local Environment Isolation**: Agent execution workspaces, intermediate scratchpads, and vector stores reside strictly within designated local project directories.
- **Automated Secret Scrubbing**: API keys, database credentials, and personal credentials are automatically redacted prior to embedding or logging.
- **Zero Commercial Monetization**: Prompts, intermediate reasoning trajectories, and task deliverables are never commercialized or shared with third parties.

---

## Limitations

Understanding the operational boundaries and technical constraints of MCP Agents is essential for effective deployment.

### 1. High-Latency Tool Discovery Over Network SSE/WebSocket Transports
- **Limitation**: Discovering large tool suites over high-latency SSE or remote WebSocket connections can delay initial query processing.
- **Mitigation**: Cache discovered tool schemas locally with a configurable TTL and perform background refresh checks.

### 2. Context Window Consumption During Parallel Multi-Agent Aggregation
- **Limitation**: Aggregating outputs from numerous concurrent sub-agents can exhaust the master orchestrator's context window.
- **Mitigation**: Synthesize worker outputs into concise structured summaries before passing them to the aggregating agent.

### 3. Non-Deterministic Evaluation Scoring in Subjective Critique Loops
- **Limitation**: LLM-based evaluators may produce variable critique scores across iterations on subjective creative writing tasks.
- **Mitigation**: Employ strict, rubric-anchored few-shot evaluation prompts and clamp iteration limits to prevent endless loops.

### 4. Stdio Child Process Lifecycle Drift and Orphaned Connections
- **Limitation**: Abnormal termination of host agent processes can leave orphaned stdio child MCP server processes running in the background.
- **Mitigation**: Implement strict process group isolation and registered shutdown signal handlers (`SIGTERM`, `SIGINT`) to reap child processes cleanly.

### 5. Blocking Human-in-the-Loop Pauses on Unattended Batch Workflows
- **Limitation**: Automated background batch jobs can stall indefinitely if an unexpected tool action requires interactive human approval.
- **Mitigation**: Configure automated fail-closed timeouts with alert webhooks or pre-approve specific read-only scopes for batch executions.

---

## Summary & Compliance Checklist

| Checkpoint 2 Requirement | Corresponding Section | Status |
| :--- | :--- | :---: |
| **How the agent decides** | [How the Agent Decides](#how-the-agent-decides) | **Covered** |
| - Decision architecture & 5-stage pipeline | Section 1 | Verified |
| - Decision logic & routing formulations | Section 2 | Verified |
| - Thresholding & refusal decision criteria | Section 3 | Verified |
| - Fallback decision mechanism | Section 4 | Verified |
| - Human-in-the-loop governance & oversight | Section 5 | Verified |
| **The data it uses** | [The Data It Uses](#the-data-it-uses) | **Covered** |
| - Ingested input data & query streams | Section 1 | Verified |
| - Configuration & reference schemas | Section 2 | Verified |
| - Base model lineage & deterministic engines | Section 3 | Verified |
| - Data privacy, retention lifecycle & MITRE/OWASP | Section 4 | Verified |
| **Its limitations** | [Limitations](#limitations) | **Covered** |
| - High-Latency Tool Discovery Over Network SSE/WebSocket Transports | Section 1 | Verified |
| - Context Window Consumption During Parallel Multi-Agent Aggregation | Section 2 | Verified |
| - Non-Deterministic Evaluation Scoring in Subjective Critique Loops | Section 3 | Verified |
| - Stdio Child Process Lifecycle Drift and Orphaned Connections | Section 4 | Verified |
| - Blocking Human-in-the-Loop Pauses on Unattended Batch Workflows | Section 5 | Verified |
