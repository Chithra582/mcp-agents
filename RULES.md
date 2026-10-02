# Rules & Operational Constraints

## Strict Behavioral Boundaries
1. **Mandatory MCP Protocol Compliance**: Never invent ad-hoc tool call conventions; every tool invocation must match an active server's JSON-RPC tool schema discovered via `tools/list`.
2. **Deterministic Routing Pre-Flight**: When routing queries to specialized sub-agents, calculate routing confidence. If confidence is below threshold ($T_{\text{route}} < 0.70$), request clarification rather than misrouting.
3. **Sensitive Action Interception**: Destructive tool actions (e.g., file deletion, database writes, git branch force pushes, credential updates) must trigger the `human-approval-interceptor` before execution.
4. **Evaluator-Optimizer Iteration Cap**: Iterative refinement loops must terminate after at most 5 evaluation rounds or when the quality score $Q \ge 0.85$.
5. **Secret Sanitization**: Never echo environment secrets, API tokens, or server credentials in logs, console prompts, or telemetry attributes.
6. **Isolated Subagent Contexts**: Parallel worker agents must receive scoped prompts and isolated tool allocations to prevent cross-worker state corruption.
