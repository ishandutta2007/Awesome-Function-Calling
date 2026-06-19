# Schema Enforcement & Strictness Variants

This document provides a detailed explanation of schema enforcement methods used to guarantee structured responses from LLMs.

---

## Validation Flow

The diagram below illustrates how structured outputs and JSON modes validate and restrict output generation compared to standard tool calling:

```mermaid
graph TD
    %% Define Styles
    classDef default fill:#1e1e2e,stroke:#cba6f7,stroke-width:2px,color:#cdd6f4;
    classDef model fill:#89b4fa,stroke:#89b4fa,stroke-width:2px,color:#11111b;
    classDef check fill:#f9e2af,stroke:#f9e2af,stroke-width:2px,color:#11111b;

    Prompt[Developer Prompt & Schema] --> LLM[LLM Output Generation]:::model
    
    LLM --> ModeSelection{Enforcement Mode}
    
    %% Standard
    ModeSelection -->|Standard Tool Use| StandardOutput[Raw JSON Output]
    StandardOutput --> Valid1{Valid JSON & Types?}:::check
    Valid1 -->|Yes| Client[Consume Data]
    Valid1 -->|No/Hallucination| Fail1[Schema/Parsing Error]

    %% JSON Mode
    ModeSelection -->|JSON Mode| TokenForce1[Force Valid JSON tokens during generation]
    TokenForce1 --> Valid2{Matches Schema?}:::check
    Valid2 -->|Yes| Client
    Valid2 -->|No/Missing Fields| Fail2[Missing Properties Error]

    %% Structured Outputs
    ModeSelection -->|Structured Outputs| GrammarConstraint[Grammar-Guided Constrained Decoding]
    GrammarConstraint --> AutoValid[Guaranteed Valid Output Structure]
    AutoValid --> Client
```

---

## Detailed Schema Variants

### 1. Standard Function Calling (Tool Use)
In standard function calling, the developer supplies the LLM with a structural JSON schema outlining the parameters and types of the tools. The model tries to generate an output that matches this schema via prompt instructions. However, because there is no programmatic constraint on the tokens generated, the model can occasionally hallucinate incorrect types, omit required properties, or produce malformed JSON.

### 2. Structured Outputs
Introduced by modern LLM APIs (e.g., OpenAI's Structured Outputs), this variant uses **grammar-guided constrained decoding** at the inference engine level. It translates the developer's JSON schema into a grammar (like Context-Free Grammars). The engine restricts the logits at each step, making it mathematically impossible for the model to output a token that violates the JSON schema. It guarantees 100% schema adherence.

### 3. JSON Mode
A predecessor to Structured Outputs. In JSON Mode, the inference engine forces the model to respond in a valid, parseable JSON format. However, unlike Structured Outputs, the model is not forced to adhere to any *specific* schema. It ensures the syntax is correct (`{}` and parsing works), but it does not guarantee that the required fields or correct property names are present in the response.
