---
name: human-in-the-loop-governance
description: Use when intercepting high-risk tool operations, managing human approval gates, and enforcing safety thresholds.
---

# Human-in-the-Loop Governance

## Overview
Provides safety and governance mechanisms to ensure high-stakes or irreversible MCP tool invocations receive human verification before execution.

## When to Use
- When invoking tools that perform write/delete actions on databases, production servers, or version control.
- When an agent detects low confidence or conflicting instructions from user input.
- When regulatory or policy compliance requires affirmative human sign-off.

## Key Mechanisms
1. **Risk Tiering**: Categorizes tool schemas into low (read-only), medium (idempotent write), and high (destructive/external) tiers.
2. **Approval Suspension**: Suspends execution via async event queues or Temporal signals while notifying operators via CLI or webhook.
3. **Fail-Closed Timeout**: Aborts the operation safely if the human reviewer does not respond within the defined timeout window.
