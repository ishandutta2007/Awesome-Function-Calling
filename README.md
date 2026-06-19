# Awesome-Function-Calling
## Function-Calling Variants in Large Language Models (LLMs)

Function-calling enables LLMs to interact with external systems, APIs, and databases. Depending on the architecture, strictness requirements, and execution needs, function-calling can be implemented through several distinct variants.

---

## 1. Execution & Complexity Variants

These variants define how many functions are processed and how they execute to complete a user query.

*   **Single Function Calling**
    *   The model processes a prompt to invoke exactly one function from a single tool definition.
*   **Multiple Function Selection**
    *   The LLM is provided with a toolbox of several distinct functions.
    *   The model analyzes user intent to select the single best tool for the task.
*   **Parallel Function Calling**
    *   The model calls a single function multiple times simultaneously within one response payload.
    *   *Example:* Checking the weather for three different cities at once.
*   **Parallel Multiple Function Calling**
    *   A advanced variant where the LLM triggers multiple *different* tools concurrently.
*   **Multi-Step / Chaining Function Calling**
    *   The LLM executes a sequential, dependent loop.
    *   It generates an initial tool call, digests the system return payload, and uses that new data to trigger a subsequent tool call.

---

## 2. Schema Enforcement & Strictness Variants

These variations dictate how rigidly the LLM must adhere to the formatting rules of the underlying APIs.

*   **Standard Function Calling (Tool Use)**
    *   The model receives a structural JSON schema detailing arguments and types.
    *   It attempts to return matched arguments but may occasionally hallucinate or omit fields.
*   **Structured Outputs**
    *   Introduced as a highly strict variant by provider APIs.
    *   Employs constrained decoding techniques to guarantee that the model's output strictly matches the specified JSON schema.
*   **JSON Mode**
    *   A lightweight precursor where no specific API schema is enforced.
    *   The model is mathematically forced to respond only in a valid, parseable JSON format.

---

## 3. Model Behavior Options (Tool Choice)

Developers can explicitly constrain how the LLM decides to interact with functions via system configurations:

*   **Auto Mode**
    *   The default behavior where the model dynamically decides whether to reply with standard text or issue tool calls.
*   **Required Mode**
    *   Forces the LLM to select and call at least one of the available tools before responding.
*   **Forced / Specific Function**
    *   Restricts the model entirely, forcing it to call one specific function regardless of the prompt nuance.

---

## 4. Integration Ecosystem Variants

*   **Custom / Injected Tooling**
    *   Used for local, open-source models that lack native function-calling layers.
    *   Tool docstrings are converted to JSON and injected into system prompts, paired with code parsers to extract the calls.
*   **Model Context Protocol (MCP)**
    *   An infrastructure layer that standardizes tool discovery and authentication.
    *   Serves as a unified middle-layer connecting models instantly to applications without rewriting unique JSON schemas for every model type.
