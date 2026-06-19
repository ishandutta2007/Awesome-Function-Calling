# Integration Ecosystem Variants

This document details the ecosystem integrations used to connect LLMs to external tools, highlighting Custom Injected Tooling and the Model Context Protocol (MCP).

---

## Architecture Comparison

The diagram below compares custom prompt injection architecture with the unified Model Context Protocol:

```mermaid
graph TD
    subgraph Custom Injected Tooling (OS Models)
        Model_C[Local LLM] <-->|Regex / Parser| App_C[Custom App Code]
        App_C <-->|Custom JSON Schema| Tool_C[Tool Execution]
    end

    subgraph Model Context Protocol (MCP)
        Model_M[Model Client / Claude] <-->|Standard Protocol| MCP_Host[MCP Host]
        MCP_Host <-->|Standard Protocol| MCP_Server[MCP Server]
        MCP_Server <-->|Standardized Schemas| Multi_Tools[Tools / APIs / DBs]
    end

    %% Styles
    classDef default fill:#1e1e2e,stroke:#cba6f7,stroke-width:2px,color:#cdd6f4;
    classDef mcp fill:#a6e3a1,stroke:#a6e3a1,stroke-width:2px,color:#11111b;
    classDef custom fill:#f9e2af,stroke:#f9e2af,stroke-width:2px,color:#11111b;

    Model_C:::custom
    App_C:::custom
    Tool_C:::custom

    Model_M:::mcp
    MCP_Host:::mcp
    MCP_Server:::mcp
```

---

## Detailed Integration Explanations

### 1. Custom / Injected Tooling
Before function-calling APIs were native to LLMs, open-source models relied on Custom / Injected Tooling. Developers write custom system instructions describing the available functions in a serialized format (e.g., XML or JSON) and append this text directly to the system prompt. A parser on the application side intercepts the model's text generation (often matching specific markers like `<tool_call>...</tool_call>`) using regex, executes the tool, and injects the output back into the conversation history.

### 2. Model Context Protocol (MCP)
The **Model Context Protocol (MCP)**, introduced by Anthropic, is an open-source protocol that standardizes how LLM clients connect to data sources and tools. Rather than writing custom integration layers for every tool and LLM, MCP establishes a client-server architecture:
- **MCP Client:** The application hosting the LLM (e.g., cursor, Claude Desktop).
- **MCP Server:** A modular service exposing specific tools, resources, or prompts via a unified JSON-RPC API.
MCP enables plug-and-play tool integration without custom parsing, schema translations, or unique middleware logic.
