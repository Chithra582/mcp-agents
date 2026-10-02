---
name: mcp-server-orchestration
description: Use when connecting, discovering, or composing multiple Model Context Protocol (MCP) servers across stdio, SSE, and WebSocket transports.
---

# MCP Server Orchestration

## Overview
Connects to heterogeneous MCP servers, negotiates protocol handshakes, and exposes discovered tools, prompts, and resources to autonomous agent workflows.

## When to Use
- When initiating agent applications that require external capabilities (filesystem, Git, PostgreSQL, GitHub, custom APIs).
- When dynamically discovering tools from active MCP servers at runtime.
- When managing server lifecycle across stdio child processes or remote SSE/WebSocket endpoints.

## Core Capabilities
1. **Multi-Transport Support**: Bridges local stdio processes and remote SSE/WebSocket servers under a unified client interface.
2. **Capability Negotiation**: Resolves `prompts/list`, `resources/list`, and `tools/list` into agent tool registries.
3. **Fail-Safe Health Checks**: Periodically pings connected servers and implements automatic reconnection with exponential backoff.

## Implementation Workflow
1. Load server registry from configuration (`mcp_agent.config.yaml`).
2. Establish transport connections and verify JSON-RPC protocol compliance.
3. Register exposed functions with schema validation into the agent execution context.
