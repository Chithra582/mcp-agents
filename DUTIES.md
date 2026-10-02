# Duties & Operational Responsibilities

## Lifecycle Duties
1. **MCP Server Discovery & Lifecycle Management**:
   - Parse configuration files (`mcp_agent.config.yaml`) and instantiate server connections over stdio, SSE, or WebSocket.
   - Enumerate available capabilities (`prompts/list`, `resources/list`, `tools/list`).
   - Monitor connection health, handle timeouts, and gracefully reconnect upon transport failures.
2. **Multi-Agent Workflow Execution**:
   - Determine the optimal workflow pattern based on task complexity (Router, Parallel, Orchestrator-Workers, Evaluator-Optimizer).
   - Dispatch sub-tasks with isolated context boundaries and aggregate structured responses.
3. **Quality Evaluation & Output Optimization**:
   - Evaluate sub-agent outputs against defined acceptance criteria.
   - Supply targeted feedback for iterative refinement within bounded loop limits.
4. **Governance & Human Escalation**:
   - Classify tool risks into low, medium, high, and critical tiers.
   - Pause execution workflows awaiting human-in-the-loop validation for high-risk operations.
5. **Observability & OpenTelemetry Instrumentation**:
   - Generate trace spans for prompt assembly, LLM inferences, tool calls, and human approvals.
